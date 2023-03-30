---
title: 'List registered SPNs in Active Directory: pimped'
author: Harold Preyers
type: post
date: 2016-05-13T08:44:11+00:00
url: /list-registered-spns-active-directory-pimped/
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Active Directory
  - Microsoft
  - PowerShell

---
# List all registered SPNs in Active Directory: pimped

This will go and poll all your registered SPNs in Active Directory and write them to a file. It accepts Debug, Log\_Dir and Log\_FileName as parameters.

Source: <http://social.technet.microsoft.com/wiki/contents/articles/18996.list-all-spns-used-in-your-active-directory.aspx>

<pre class="lang:ps decode:true">&lt;#
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

#&gt;

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