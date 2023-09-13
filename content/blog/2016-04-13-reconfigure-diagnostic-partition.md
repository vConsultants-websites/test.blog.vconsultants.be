+++
author = "Harold Preyers"
title = "Reconfigure diagnostic partition"
date = "2016-04-13"
description = "Leverage powercli to execute esxcli commands remote."
featured = true
tags = [
    "esxcli",
    "esxi",
    "powercli"
]
categories = [
    "VMware",
    "vSphere",
    "powercli"
]
+++
# Reconfigure diagnostic partition with PowerCLI using Get-EsxCli

The following Get-EsxCli command will unconfigure your diagnostic partition and reconfigure with smart selection. This was needed because the install partition uuid had changed due to an option in the NetApp system while doing system testing.

```powershell
$server_list = Get-VMhost


Foreach ($srv in $server_list)
{
 $esxcli = Get-EsxCli -VMhost $srv
 #$esxcli.system.coredump.file.add($null,"VMFS_log_partition","$srv.name",$null)
 $esxcli.system.coredump.partition.set($null,$null,$null,$true)
 $esxcli.system.coredump.partition.set($true,$null,$true,$null)
 $esxcli.system.coredump.partition.get()
}
```

Many thanks to <http://www.virten.net/2014/02/howto-use-esxcli-in-powercli/>