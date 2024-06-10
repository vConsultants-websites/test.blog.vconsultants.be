+++
author = "Harold Preyers"
title = "Powershell Test AlwaysOn"
date = "2016-04-15"
description = ""
featured = false
tags = [
    "microsoft",
    "powershell",
    "sql-server-2012"
]
categories = [
    "Microsoft",
    "PowerShell",
    "SQL Server 2012"
]
+++

# Powershell Test AlwaysOn

This script checks which node is the primary in the SQL AlwaysOn cluster. A test database has been added to see if that database is writable. All actions are logged to a log file.

{{% notice warning "Warning" %}}
This script is **not to be run** in a production environment.
{{% /warning %}}

The purpose of this script is to see if the SQL AlwaysOn Availability Group has done a failover. It also checks if a database within this AG is writable and if not how long it has been offline.

{{% notice info "Info" %}}
The part to check which node is the primary has been found on a blog, which I would have given the credits if I remembered where I found it.
{{% /info %}}

```powershell

.Synopsis
Monitors an SQL AlwaysOn cluster and tries to write to a database on this AlwaysOn cluster.

.Description
Monitors an SQL AlwaysOn cluster and tries to write to a database on this AlwaysOn cluster.

.Example
# No Examples available

# Import SQL module
Import-Module sqlps

# Connection parameters
$ServerInstance="database_server" # if you are running SQL on a custom port it should be "database_server,port"
$Database="TEST_AlwaysOn"

# Logging
$FileName="PrimaryLOG"
$LogDir="E:\SQL_script"
$LogBreaker="##########################"
$global:File=""
$MaxLogEntries = 1000
$LogEntries = 1
$Failed = 0
$Sleep = 900

Function Initialize {
	# Logging parameters
	$LogFileDate = Get-Date -Format FileDateTime
	$global:File = $LogDir + "\" + $FileName + "_" + $LogFileDate + ".log"
	If ($Debug) {write "The filename is: $global:File"}
 
	# Initialization (empty table)
	$SQLQuery=$("TRUNCATE TABLE Test_AlwaysOn.dbo.Test_Table")
	invoke-sqlcmd -query $SQLquery -serverinstance $ServerInstance -database $Database
	#Start-Sleep -Seconds 3
 
	# Initialize log
	$LogBreaker | Out-File "$global:File" -Append
	" LogFile: $global:File" | Out-File "$global:File" -Append
	" LogDate: $Date" | Out-File "$global:File" -Append
	" Server: $ServerInstance" | Out-File "$global:File" -Append
	" Database: $Database was truncated" | Out-File "$global:File" -Append
	$LogBreaker | Out-File "$global:File" -Append
}

Initialize

# MAIN
# While statement is built to always run
While ($true) {
	$Date = (get-date).ToString("yyyy-MM-dd HH:mm:ss.fff")
	# Generate at least one entry/day to see if I'm alive
	If ((get-date -Format hhmmss) -eq 000000) {
		$AddToFile = "$Date" + " It's a new day, it's a new dawn"
		$AddToFile | Out-File "$global:File" -Append
	}
	$Run++
 
	# Find AlwaysOn Primary
	$SQLQuery=$("SELECT AGC.name, RCS.replica_server_name, ARS.role_desc, AGL.dns_name " +
	"FROM sys.availability_groups_cluster AS AGC " +
	"INNER JOIN sys.dm_hadr_availability_replica_cluster_states AS RCS " +
	"ON RCS.group_id = AGC.group_id " +
	"INNER JOIN sys.dm_hadr_availability_replica_states AS ARS " +
	"ON ARS.replica_id = RCS.replica_id " +
	"INNER JOIN sys.availability_group_listeners AS AGL " +
	"ON AGL.group_id = ARS.group_id " +
	"WHERE ARS.role_desc = 'PRIMARY' ;")
	$AGResult=invoke-sqlcmd -query $SQLquery -serverinstance $ServerInstance -database $Database
	$PrimaryReplica = $AGResult[1]
 
	If ($PrimaryReplica -ne $PrimaryReplicaLastRun) {
		# Filter first run
		If ($PrimaryReplicaLastRun -eq $null) {
			$AddToFile = "$Date" + " AG is $PrimaryReplica"
			$AddToFile | Out-File "$global:File" -Append
			$LogEntries++
		}
		Else {
			# Write to log
			$AddToFile = "$Date" + " AG changed from $PrimaryReplicaLastRun to $PrimaryReplica"
			$AddToFile | Out-File "$global:File" -Append
			$LogEntries++
		}
	}

	# Write to table to see if it is available
	$SQLQuery=$("INSERT INTO Test_AlwaysOn.dbo.Test_Table (Test_Run,AG_Primary,Test_Time) " +
	"VALUES ('$Run','$PrimaryReplica','$Date'); " )
	invoke-sqlcmd -query $SQLquery -serverinstance $serverinstance -database $database

	# Read the last insert via $Run
	$SQLQuery=$("SELECT Test_Run,AG_Primary,Test_Time FROM Test_AlwaysOn.dbo.Test_Table where Test_run = $Run")
	$SelectResult=invoke-sqlcmd -query $SQLquery -serverinstance $serverinstance -database $database
	If ($Debug) {write "SelectResult: "$SelectResult}
	$AG_Primary = $SelectResult[1]
	$Test_Time = $SelectResult[2].ToString("yyyy-MM-dd HH:mm:ss.fff")

	# Write to log
	If ($Test_Time -eq $Date) {
		If ($Failed -gt 0) {
			$AddToFile = "$Date Database was not writable between $Test_TimeLastSucceed and $Date, we tried $Failed times."
			$AddToFile | Out-File "$global:File" -Append
			$LogEntries++
			$OfflineHR = $Date.Substring(11,2) - $Test_TimeLastSucceed.Substring(11,2)
			If ($Debug) {write $OfflineHR}
			$OfflineMin = $Date.Substring(14,2) - $Test_TimeLastSucceed.Substring(14,2)
			If ($Debug) {write $OfflineMin}
			$OfflineSec = $Date.Substring(17,5) - $Test_TimeLastSucceed.Substring(17,5)
			If ($Debug) {write $OfflineSec}
			$Offline = $OfflineHR*3600 + $OfflineMin*60 + $OfflineSec
			If ($Debug) {write $Offline}
			$AddToFile = "$Date Database was not writable for $Offline seconds."
			$AddToFile | Out-File "$global:File" -Append
			$LogEntries++
			$Failed = 0
		}
		$Test_TimeLastSucceed = $Test_Time
		$Test_Time = $null
	}
	Else {
		$Failed++
	}
	Start-Sleep -Milliseconds $Sleep

	# Check if the # entries have reached the maximum entries
	If ($LogEntries % $MaxLogEntries -eq 0) {
		$LogBreaker | Out-File "$global:File" -Append
		$AddToFile = "$Date" + " Log limits have been reached. A new log file will be started."
		$AddToFile | Out-File "$global:$File" -Append
		Initialize
	}

	# Keep the values of the variables of the last run
	$PrimaryReplicaLastRun = $PrimaryReplica
}

```