---
title: 'List registered SPNs in Active Directory: pimped'
author: 'Harold Preyers'
type: post
date: '2016-05-13'
url: /list-registered-spns-active-directory-pimped/
categories:
  - Active Directory
  - Microsoft
  - PowerShell

---

This will go and poll all your registered SPNs in Active Directory and write them to a file. It accepts Debug, Log\_Dir and Log\_FileName as parameters.

Source: <http://social.technet.microsoft.com/wiki/contents/articles/18996.list-all-spns-used-in-your-active-directory.aspx>

```powershell
<#
	.Synopsis
	Go and poll all your SPNs registered in Active Directory and write them to a file

	.Description
	Go and poll all your SPNs registered in Active Directory and write them to a file.

	.Author
	Harold Preyers

	.Parameter Debug
	The Debug parameter shows output to the screen.

	.Parameter Log_Dir
	Supply your own LogFile location.

	.Parameter Log_FileName
	Supply your own LogFile location. Don't add a file extension, the script will construct a filename based on the time of launch and add a .log extension

	.Example
	# Start SPN_List.ps1 without debug output based on the default log file location
	./SPN_List.ps1

	.Example
	# Start storage vmotions with debug output based on the default log file location
	./SPN_List.ps1 -Debug $true 

	.Example
	# Start storage vmotions with debug output based on custom log file location
	./SPN_List.ps1 -Debug $true -Log_Dir "C:\Scripts\SPN_List\Logs" -Log_FileName SPN_list 

#>

# Accept parameters
[cmdletBinding()]
	Param (
		[Bool]$Debug = $false				# Write output to console
		[string]$Log_Dir,					# Log Directory location
		[string]$Log_FileName,				# Log Filename
	)

#Variables
$Break = "-------------------------"

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
	Add-Content -Path c:\temp\SPN_List.txt -Value "`n"
}

#Set Search
cls
$search = New-Object DirectoryServices.DirectorySearcher([ADSI]“”)
$search.filter = “(servicePrincipalName=*)”
$Results = $search.Findall()

#list results
Foreach($Result in $Results)
{
	$userEntry = $result.GetDirectoryEntry()

	$Output = "Object Name = " + $userEntry.name
	If ($Debug) {Write-Host $Output -backgroundcolor "yellow" -foregroundcolor "black"}
	$Output | Out-File c:\temp\SPN_List.txt -Append
	$Break | Out-File c:\temp\SPN_List.txt -Append

	$Output = "DN = " + $userEntry.distinguishedName
	If ($Debug) {Write-host $Output}
	$Output | Out-File c:\temp\SPN_List.txt -Append

	$Output = "Object Cat. = " + $userEntry.objectCategory
	If ($Debug) {Write-host $Output}
	$Output | Out-File c:\temp\SPN_List.txt -Append
	$Break | Out-File c:\temp\SPN_List.txt -Append

	$Output = "servicePrincipalNames"
	If ($Debug) {Write-host $Output}
	$Output | Out-File c:\temp\SPN_List.txt -Append
	$Break | Out-File c:\temp\SPN_List.txt -Append

	$i=1

	foreach($SPN in $userEntry.servicePrincipalName) {
		If (($i).tostring().length -le 1) {
			$preZero = "0"
		}
		Else {
			$preZero = ""
		}
		$Output = "SPN(" + $preZero + $i + ") = " + $SPN
		If ($Debug) {Write-host "SPN(" $preZero$i ") = " $SPN}
		$Output | Out-File c:\temp\SPN_List.txt -Append
		$i+=1
	}
	If ($Debug) {Write-host ""}
	$Break | Out-File c:\temp\SPN_List.txt -Append
	Add-Content -Path c:\temp\SPN_List.txt -Value "`n"
}</pre>

&nbsp;