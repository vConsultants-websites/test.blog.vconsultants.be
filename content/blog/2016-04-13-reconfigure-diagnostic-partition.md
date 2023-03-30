---
title: Reconfigure diagnostic partition
author: Harold Preyers
type: post
date: 2016-04-13T09:12:07+00:00
url: /reconfigure-diagnostic-partition/
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - PowerCLI
  - VMware
  - vSphere
tags:
  - esxcli
  - esxi
  - powercli

---
# Reconfigure diagnostic partition with PowerCLI using Get-EsxCli

The following Get-EsxCli command will unconfigure your diagnostic partition and reconfigure with smart selection. This was needed because the install partition uuid had changed due to an option in the NetApp system while doing system testing.

<pre class="lang:ps decode:true ">$server_list = Get-VMhost

Foreach ($srv in $server_list)
{
 $esxcli = Get-EsxCli -VMhost $srv
 #$esxcli.system.coredump.file.add($null,"VMFS_log_partition","$srv.name",$null)
 $esxcli.system.coredump.partition.set($null,$null,$null,$true)
 $esxcli.system.coredump.partition.set($true,$null,$true,$null)
 $esxcli.system.coredump.partition.get()
}</pre>

Many thanks to <http://www.virten.net/2014/02/howto-use-esxcli-in-powercli/>