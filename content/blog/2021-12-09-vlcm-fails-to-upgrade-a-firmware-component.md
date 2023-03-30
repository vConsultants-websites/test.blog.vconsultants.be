---
title: vLCM fails to upgrade a firmware component
author: Harold Preyers
type: post
date: 2021-12-09T12:09:25+00:00
excerpt: I recently experienced an issue within a HPE environment where vSphere Lifecycle Management fails to upgrade the firmware on a HP FlexFabric 534FLR-SFP+ Adapter.
url: /vlcm-fails-to-upgrade-a-firmware-component/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/12/vLCM_Image_settings.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - VMware
tags:
  - firmware
  - HPE
  - vLCM
  - vsphere

---
<p class="has-text-align-justify">
  I recently experienced an issue within a HPE environment where vSphere Lifecycle Management (vLCM) fails to upgrade the firmware on a HP FlexFabric 534FLR-SFP+ Adapter.
</p>

<p class="has-text-align-justify">
  On HPE Gen10 servers it is possible to leverage vSphere LifeCycle Management to manage not only the ESXi version but also the firmware and drivers of the different hardware components. vLCM leverages a vendor tool, in HPE&#8217;s case it is either HP OneView or HP Amplifier, to do the lift and shift for the firmware.
</p>

<p class="has-text-align-justify">
  Apparently it fails when there are multiple adapters present in the system which have a firmware v7.15.97 or prior. The upgrade would succeed on one adapter but not on the subsequent adapter(s), see <a href="https://support.hpe.com/hpesc/public/docDisplay?docId=a00050180en_us&docLocale=en_US" target="_blank" rel="noreferrer noopener">here</a>. The KB is specifically mentioning HP OneView but as I experienced it is also affecting HP Amplifier, which makes sense.
</p>

<p class="has-text-align-justify">
  The following screenshot shows two hosts out of compliance with the image, because of that specific firmware. Other hosts in that cluster upgraded the firmware on the adapter just fine. It really is due to the version to upgrade from.
</p><figure class="wp-block-image size-large">

[<img decoding="async" loading="lazy" width="1024" height="899" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/12/vLCM_screenshot.png?resize=1024%2C899&#038;ssl=1" alt="vLCM Cluster Image settings and Compliance" class="wp-image-2456" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/12/vLCM_screenshot.png?resize=1024%2C899&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/12/vLCM_screenshot-980x860.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/12/vLCM_screenshot-480x421.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" />][1]</figure> 

### Resolution

<p class="has-text-align-justify">
  The article is providing a link to a firmware upgrade utility, which is for ESXi 6.0 / 6.5. You can download the 7.0 version <a href="https://support.hpe.com/hpesc/public/swd/detail?swItemId=MTX_8aa2bb46cf68452bbc375a4a74" target="_blank" rel="noreferrer noopener">here</a>.
</p>

Now that we downloaded the firmware update utility, put the host into Maintenance Mode and copy it onto the ESXi host. Putting it in the /tmp directory gives the (dis)advantage that is tis removed when the machine is rebooted.

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " title="Secure copy command">scp CP049023.zip root@host_fqdn:/tmp</pre>
</div>

SSH to the host and install the firmware update utility (Smart Component):

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " title="Install the firmware update utility">cd /tmp
mkdir CP049023
unzip CP049023.zip -d ./CP049023
esxcli software component apply -d /tmp/CP049023/CP049023_VMw.zip</pre>
</div>

This should be the output:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " title="Installation result ">Installation Result
   Components Installed: Smart-Component-CP049023_1.28.50.6-7.0.0.15843807
   Components Removed:
   Components Skipped:
   Message: Operation finished successfully.
   Reboot Required: false</pre>
</div>

Now go to the directory where the firmware update utility is installed and run it:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " title="Navigate to the location where the fuu is installed and run it">cd /opt/Smart_Component/CP049023
./Execute_Component</pre>
</div>

This should be the output:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " title="The fuu output">Command [ ./Execute_Component ]
Number of parameters passed in [ 0 ]
The parameters are [  ]
OS Version found  [7.0.2]
Process [7.0.2] with path [./ESXi_7.0]
Set Flash Engine files for path [./ESXi_7.0]
... leaving ./determine_which_OS.sh in /opt/Smart_Component/CP049023 ...
execute hpsetup with parameters [  ]

===============================================================
HPE QLogic NX2 Online Firmware Upgrade Utility for VMware
Version: 1.28.50

Performing Discovery operation......Please be patient..

Selecting HP FlexFabric 10Gb 2-port 534FLR-SFP+ Adapter MAC: 1458D041DB10
Update MBI 7.10.72 to 7.18.80 y/n/q (y):y

Firmware update in progress......It will take a while....Please be patient..

Please reboot for the firmware flash to complete.

... END [ ./Execute_Component - Return value is 1 ] ...</pre>
</div>

If the Return value is 1, that is a good sign. I had to rerun it some times because of return value 0. I also had a return value of 106, which didn&#8217;t change after several runs. I rebooted that host, ran it again and then it went ok.

As a final step clean up the actions, so remove the firmware update utility:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " >esxcli software component remove -n Smart-Component-CP049023
Removal Result
   Components Installed:
   Components Removed: Smart-Component-CP049023_1.28.50.6-7.0.0.15843807
   Components Skipped:
   Message: Operation finished successfully.
   Reboot Required: false</pre>
</div>

Reboot. When the host is back, Check Compliance again and you should be good to go.

 [1]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/12/vLCM_screenshot.png?ssl=1