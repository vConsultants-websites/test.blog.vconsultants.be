---
title: PowerCLI goodies
author: Harold Preyers
type: post
date: 2016-04-12T09:14:16+00:00
url: /powercli-55-goodies/
vantage_panels_no_legacy:
  - 'true'
siteorigin_page_settings:
  - 'a:6:{s:6:"layout";s:7:"default";s:10:"page_title";b:1;s:15:"masthead_margin";b:1;s:13:"footer_margin";b:1;s:13:"hide_masthead";b:0;s:19:"hide_footer_widgets";b:0;}'
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - PowerCLI
  - VMware
tags:
  - esxi
  - powercli
  - powercli-snippet
  - vsphere

---
These are powercli goodies&nbsp;I use on a regular base. I have collected them here to find them easily. Some I wrote myself, some are copied from other sites. If I didn&#8217;t reference the source, I don&#8217;t know anymore where I found it.

## Change portgroup on a lot of vm&#8217;s

Testing with one vm

<pre class="wp-block-code lang:ps decode:true"><code>get-vm my_vm_name&nbsp;| Get-NetworkAdapter |where {$_.networkname -eq "current_network_label"} | set-networkadapter -portgroup "new_network_label" -confirm:$false</code></pre>

Reconfigure all vm&#8217;s with the string &#8220;vdi&#8221; in the name

<pre class="wp-block-code lang:ps decode:true"><code>get-vm *vdi*&nbsp;| Get-NetworkAdapter |where {$_.networkname -eq "current_network_label"} | set-networkadapter -portgroup "new_network_label" -confirm:$false</code></pre>

### Find all vm&#8217;s with more than 8 CPUs

<pre class="wp-block-code lang:ps decode:true"><code>(get-view -viewtype virtualmachine).summary.config | where {$_.numcpu -gt 8}</code></pre>

### Reload Syslog via esxcli

While executing the NetApp MetroCluster testplan, the syslog service stops logging to the presented syslog datastore. To restart the logging&nbsp;reload the syslog service on all&nbsp;impacted hosts. The following command will reload the syslog service on&nbsp;all&nbsp;hosts in the connected vCenters.&nbsp;Check the $global:defaultviservers to know which vCenters are connected.

<pre class="wp-block-code lang:ps decode:true"><code>$server_list&nbsp;= Get-VMhost

Foreach ($srv in $server_list)
{
   $esxcli = Get-EsxCli -VMhost $srv
   $esxcli.system.syslog.reload()
}</code></pre>

### Speed-up the initialization of PowerCLI

This needs to be done for each registered version of PowerCLI. This one worked for me on Windows Server 2012 R2

C:\Windows\Microsoft.NET\Framework64\v4.0.30319\ngen.exe install “VimService55.XmlSerializers, Version=5.5.0.0, Culture=neutral, PublicKeyToken=10980b081e887e9f” /ExeConfig:c:\windows\system32\WindowsPowerShell\v1.0\PowerShell_ISE.exe

### Change the Power Management Policy for each host in cluster

<pre class="wp-block-code lang:ps decode:true"><code>#get connected esxi hosts
$vmhostlist = get-vmhost |sort|select name

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
}</code></pre>

#### Update:

Apparently there were some errors (curly brackets missing or in the wrong place) in the previous code.

It also ran several times per host because per host there was a Get-VMHost in the ForEach iteration. So If you had three hosts it would run three times per host.

The&nbsp;updated and optimized code:

<pre class="wp-block-code lang:ps"><code>$VMHosts = Get-VMHost

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

</code></pre>

### show connected vcenters/esxi&#8217;s

<pre class="wp-block-code lang:ps"><code>$Global:DefaultVIServers</code></pre>

### connect to previously connected vcenters/esxi&#8217;s

<pre class="wp-block-code lang:ps"><code>connect-viserver -menu</code></pre>

### move vm storage vmotion

<pre class="wp-block-code lang:ps"><code>get-vm Win* | move-vm -Datastore (Get-Datastore sdc-t*)</code></pre>

### Mounting and unmounting&nbsp;NFS datastores

Mount NFS datastore

<pre class="wp-block-code lang:ps decode:true"><code>get-vmhost | New-Datastore -Nfs -Name Datastore_Name -Path /vol/Name_on_NFS_host -NfsHost NFS_host_IP</code></pre>

Remove NFS datastore

<pre class="wp-block-code lang:ps decode:true"><code>get-vmhost | Remove-Datastore -datastore Datastore_Name</code></pre>

### Change the default &#8216;ESX Admins&#8217; AD group to your group name

ESXi servers will by default search for the group &#8216;ESX Admins&#8217; in Active Directory. The following command will change this to &#8216;my\_group\_name&#8217;.

<pre class="wp-block-code lang:ps decode:true"><code>get-vmhost | Get-AdvancedSetting "Config.HostAgent.plugins.hostsvc.esxAdminsGroup" | Set-AdvancedSetting -value "my_group_name" -Confirm:$false</code></pre>