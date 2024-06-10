+++
author = "Harold Preyers"
title = "PowerCLI goodies"
date = "2016-04-12"
description = "These are powercli goodies I use on a regular base."
featured = true
tags = [
    "esxi",
    "powercli",
    "powercli-snippet",
    "vsphere"
]
categories = [
    "PowerCLI",
    "VMware",
]
thumbnail = "images/building.png"
+++

These are powercli goodies I use on a regular base. I have collected them here to find them easily. Some I wrote myself, some are copied from other sites. If I didn't reference the source, I don't know anymore where I found it.

## Change portgroup on a lot of vm's

Testing with one vm

```powershell
get-vm my_vm_name | Get-NetworkAdapter |where {$_.networkname -eq "current_network_label"} | set-networkadapter -portgroup "new_network_label" -confirm:$false
```

Reconfigure all vm's with the string "vdi" in the name

```powershell
get-vm *vdi* | Get-NetworkAdapter | where {$_.networkname -eq "current_network_label"} | set-networkadapter -portgroup "new_network_label" -confirm:$false
```

### Find all vm's with more than 8 CPUs

```powershell
(get-view -viewtype virtualmachine).summary.config | where {$_.numcpu -gt 8}
```

### Reload Syslog via esxcli

While executing the NetApp MetroCluster testplan, the syslog service stops logging to the presented syslog datastore. To restart the logging, reload the syslog service on all impacted hosts. The following command will reload the syslog service on all hosts in the connected vCenters. Check the $global:defaultviservers to know which vCenters are connected.

```powershell
$server_list = Get-VMhost

Foreach ($srv in $server_list)
{
   $esxcli = Get-EsxCli -VMhost $srv
   $esxcli.system.syslog.reload()
}
```

### Speed-up the initialization of PowerCLI

This needs to be done for each registered version of PowerCLI. This one worked for me on Windows Server 2012 R2

C:\Windows\Microsoft.NET\Framework64\v4.0.30319\ngen.exe install “VimService55.XmlSerializers, Version=5.5.0.0, Culture=neutral, PublicKeyToken=10980b081e887e9f” /ExeConfig:c:\windows\system32\WindowsPowerShell\v1.0\PowerShell_ISE.exe

### Change the Power Management Policy for each host in cluster

```powershell
#get connected esxi hosts
$vmhostlist = get-vmhost | sort | select name

#for each host show the power management policy setting
foreach ($entry in $vmhostlist) {
   #list power management policy on all connected esxi hosts
   get-vmhost |sort|select name, @{ N="CurrentPolicy"; E={$_.ExtensionData.config.PowerSystemInfo.CurrentPolicy.ShortName}},@{N="CurrenPolicyKey"; E={$_.ExtensionData.config.PowerSystemInfo.CurrentPolicy.Key}},@{N="AvailablePolicies";E={$_.ExtensionData.config.PowerSystemCapability.AvailablePolicy.ShortName}
}

#for each host change the power management policy to high performance
foreach ($entry in $vmhostlist) {
   $view=(get-vmhost $entry.name|get-view);(get-view $view.configmanager.powersystem).configurepowerpolicy(1)}
   #list power management policy on all connected esxi hosts
   get-vmhost |sort|select name, @{ N="CurrentPolicy"; E={$_.ExtensionData.config.PowerSystemInfo.CurrentPolicy.ShortName}},@{N="CurrenPolicyKey"; E={$_.ExtensionData.config.PowerSystemInfo.CurrentPolicy.Key}},@{N="AvailablePolicies";E={$_.ExtensionData.config.PowerSystemCapability.AvailablePolicy.ShortName}
}
```

#### Update:

Apparently there were some errors (curly brackets missing or in the wrong place) in the previous code.

It also ran several times per host because per host there was a Get-VMHost in the ForEach iteration. So If you had three hosts it would run three times per host.

The updated and optimized code:

```powershell
$VMHosts = Get-VMHost

#for each host show the power management policy setting
ForEach ($entry in $VMHosts) {
   #list power management policy on all connected esxi hosts
   $entry | Select Name, @{ N="CurrentPolicy"; E={$_.ExtensionData.Config.PowerSystemInfo.CurrentPolicy.ShortName}},@{N="CurrenPolicyKey"; E={$_.ExtensionData.Config.PowerSystemInfo.CurrentPolicy.Key}},@{N="AvailablePolicies";E={$_.ExtensionData.Config.PowerSystemCapability.AvailablePolicy.ShortName}}
}

#for each host change the power management policy to high performance
ForEach ($entry in $VMHosts) {
   $view=($entry | Get-View);(Get-View $view.ConfigManager.PowerSystem).ConfigurePowerPolicy(1)
   #list power management policy on all connected esxi hosts
   $entry | Select Name, @{ N="CurrentPolicy"; E={$_.ExtensionData.Config.PowerSystemInfo.CurrentPolicy.ShortName}},@{N="CurrenPolicyKey"; E={$_.ExtensionData.Config.PowerSystemInfo.CurrentPolicy.Key}},@{N="AvailablePolicies";E={$_.ExtensionData.Config.PowerSystemCapability.AvailablePolicy.ShortName}}
}
```

### show connected vcenters/esxi's

```powershell
$Global:DefaultVIServers
```

### connect to previously connected vcenters/esxi's

```powershell
connect-viserver -menu
```

### move vm storage vmotion

```powershell
<pre class="wp-block-code lang:ps"><code>get-vm Win* | move-vm -Datastore (Get-Datastore sdc-t*)</code></pre>
```

### Mounting and unmounting NFS datastores

Mount NFS datastore

```powershell
get-vmhost | New-Datastore -Nfs -Name Datastore_Name -Path /vol/Name_on_NFS_host -NfsHost NFS_host_IP
```

Remove NFS datastore

```powershell
get-vmhost | Remove-Datastore -datastore Datastore_Name
```

### Change the default 'ESX Admins' AD group to your group name

ESXi servers will by default search for the group 'ESX Admins' in Active Directory. The following command will change this to 'my_group_name'.

```powershell
get-vmhost | Get-AdvancedSetting "Config.HostAgent.plugins.hostsvc.esxAdminsGroup" | Set-AdvancedSetting -value "my_group_name" -Confirm:$false
```