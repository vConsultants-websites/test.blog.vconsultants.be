+++
author = "Harold Preyers"
title = "Storage vMotions based on CSV file"
date = "2016-04-22"
description = ""
featured = false
tags = [
    "microsoft",
    "powershell",
    "powercli",
    "vmware",
    "vsphere"
]
categories = [
    "Microsoft",
    "PowerShell",
    "PowerCLI",
    "VMware",
    "vSphere"
]
+++
 
This script reads a CSV file and executes storage vmotions based on the input. The number of storage vmotions can be throttled and is by default set to maximum 6 concurrent vMotions. If the VM is already on the correct location it will not execute a storage vmotion for that vm.

This is still work in progress. The parameters to accept a different CSV file, log file location and number of storage vmotions other than the default have not been added yet.

{{% notice note "Note" %}}
This script has been built and tested on a **VMware vSphere 5.5** environment.
{{% /notice %}}

{{% notice info "**UPDATE : 2016-May-13**" %}}

I updated the script to accept all parameters as shown in the description and I also added a Dry Run parameter. The Dry Run parameter will do a dry run escaping the vMotion sequence, so all actions will be logged. Combining the Debug and DryRun parameter will show everything on the console without executing.
{{% /notice %}}


The default location for the CSV file is C:\Scripts\Storage vMotion\Sources. The script will log to the C:\Scripts\Storage vMotion\Logs location. If the directory doesn't exist, it will be created.

```powershell
.Synopsis
Moves VMs to the correct datastore based on a csv file.

.Description
Moves VMs to the correct datastore based on a csv file.

.Author
Harold Preyers

.Parameter DryRun
The DryRun parameter will create the log file without vMotion-ing the virtual machines to the correct datastore.
	
.Parameter Debug
The Debug parameter shows output to the screen. This will not prevent to start the vMotions.

.Parameter CSV_Dir
Supply your own CSV file location.

.Parameter CSV_FileName
Supply your own CSV file location.

.Parameter Log_Dir
Supply your own LogFile location.

.Parameter Log_FileName
Supply your own LogFile location. Don't add a file extension, the script will construct a filename based on the time of launch and add a .log extension

.Example
# Start storage vmotions without debug output based on the default CSV (C:\Scripts\Storage vMotion\Sources) and log file (C:\Scripts\Storage vMotion\Logs) location
# Name,Datastore
# VM1,DS1
# VM2,DS2
./svMotion_CSV.ps1

.Example
# Start storage vmotions with debug output based on the default CSV (C:\Scripts\Storage vMotion\Sources) and log file (C:\Scripts\Storage vMotion\Logs) location
# Name,Datastore
# VM1,DS1
# VM2,DS2
./svMotion_CSV.ps1 -Debug $true

.Example
# Start storage vmotions with debug output based on custom CSV and log file location
# The CSV file should have the following lay-out
# Name,Datastore
# VM1,DS1
# VM2,DS2
./svMotion_CSV -Debug $true -Log_Dir "C:\Scripts\Storage vMotion\Logs" -Log_FileName svMotion_log -CSV_Dir "C:\Scripts\Storage vMotion\Sources" -CSV_File svMotion.csv

# Accept parameters
[cmdletBinding()]
	Param (
		[Bool]$Debug = $false			# Write output to console
		[Bool]$DryRun = $false			# Write logfile without vMotion
		[string]$CSV_Dir,			# CSV Directory location
		[string]$CSV_FileName,			# CSV Filename
		[string]$Log_Dir,			# Log Directory location
		[string]$Log_FileName,			# Log Filename
	)

# Variables
$Num_vMotions = 6
$CSV_Dir = "C:\Scripts\Storage vMotion\Sources"
$CSV_FileName = "vmdatastore.csv"
$CSV_File = "$CSV_Dir\$CSV_FileName"

# Logging
$Log_Dir="C:\Scripts\Storage vMotion\Logs"
$Log_FileName="svMotion_log"
$Log_Breaker="##########################"
$global:File=""

# Create the necessary directories if necessary
If(!(Test-Path -Path $Log_Dir )){
	New-Item -ItemType directory -Path $Log_Dir
}

Function Initialize {
	# Logging parameters
	$Date = (get-date).tostring("yyyyMMdd_HHmmss")
	$global:File = $LogDir + "\" + $Log_FileName + "_" + $Date + ".log"
	If ($Debug) {Write-Host "The filename is: $global:File"}

	# Initialize log
	$Log_Breaker | Out-File "$global:File" -Append
	" LogFile:  $global:File" | Out-File "$global:File" -Append
	" LogDate:  $Date" | Out-File "$global:File" -Append
	" CSV File: $CSV_File" | Out-File "$global:File" -Append
	$Log_Breaker | Out-File "$global:File" -Append
	Add-Content -Path $global:File -Value "`n"
}

# Import CSV
$CSV = Import-csv "$CSV_File"

Initialize

$Date = (get-date).ToString("yyyy-MM-dd HH:mm:ss.fff")

# Do for each entry in CSV file
Foreach ($VM in $CSV) {
	Try {
		# Does the virtual machine exist
		Get-VM $VM.Name -ErrorAction Stop | Out-Null
		
		# What is the current Datastore of the virtual machine
		$CurrentDS=(get-vm $VM.Name | Get-Datastore).Name
		If ($CurrentDS -ne $VM.Datastore) {
			# Log & Debug
			$Message = "Starting for VM " + $VM.Name + " to datastore " + $VM.Datastore
			If ($Debug) {Write-Host "$Message" -Foregroundcolor darkyellow}
			$Message | Out-File "$global:File" -Append

			If ($DryRun -eq $false) {
				# Determing amount of vMotions currently executing
				$num_tasks = (get-task |where {$_.name -eq "RelocateVM_task"} | where {$_.state -eq "running"})
				Do {
					$num_tasks = (get-task |where {$_.name -eq "RelocateVM_task"} | where {$_.state -eq "running"})
				} While ($num_tasks.Count -eq $Num_vMotions)
				Get-VM $VM.Name | Move-VM -Datastore $VM.DS -RunAsync
			}
		}
		# virtual machine was found on the correct datastore
		$Message = $VM.Name + " is already on datastore " + $VM.Datastore
		If ($Debug) {Write-Host "$Message" -Foregroundcolor darkgreen}
		$Message | Out-File "$global:File" -Append
	}
	Catch {
		$Message = $VM.Name + " was not found."
		If ($Debug) {Write-Host "$Message" -Foregroundcolor red}
		$Message | Out-File "$global:File" -Append
	}
}