---
title: Reconfigure diagnostic partition using Get-EsxCli -V2
author: Harold Preyers
type: post
date: 2018-11-01T23:11:42+00:00
url: /reconfigure-diagnostic-partition-using-get-esxcli-v2/
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - ESXi
  - PowerCLI
  - VMware
tags:
  - esxcli
  - esxi
  - powershell

---
The following powershell snippet is going to unconfigure the diagnostic coredump partition using the esxcli version 2 cmdlet. The second part will reconfigure the diagnostic partition with the &#8216;smart&#8217; option so that an accessible partition is chosen.

If you want to configure a new diagnostic partition the you will find the necessary information in the following VMware knowledge base article: [Configuring a diagnostic coredump partition on an ESXi 5.x/6.x host (2004299)][1]. There will be additional steps to supply the partition id.

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

First we connect to the esxi host directly and insert the connection details in the variable $srv:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:1 decode:true " >$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

Then we create a esxcli object $esxcli using the variable $srv we created previously:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:2 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

Now we create a variable $arg to store the arguments we will provide later:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:3 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

Setting the $arg property ‘unconfigure’ to true will deactivate the diagnostic partition:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:4 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

The invoke command will invoke the command remotely on the esxi host. After execution the diagnostic partition is deactivated:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:5 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

The second part starts with creating a new set of arguments:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:6 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

Reactivate the coredump, because we deactivated it before:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:7 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

Enable the coredump partition:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:8 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

The ‘smart’ property will try to use an accessible partition:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:9 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

The last argument will configure the diagnostic partition using the supplied parameters:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps range:10 decode:true ">$srv = Get-VMHost ESXiHost
$esxcli = Get-EsxCli -VMHost $srv -V2
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)
$arg = $esxcli.system.coredump.partition.set.CreateArgs()
$arg.unconfigure = "false"
$arg.enable = "true"
$arg.smart = "true"
$esxcli.system.coredump.partition.set.Invoke($arg)</pre>
</div>

 [1]: https://kb.vmware.com/s/article/2004299