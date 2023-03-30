---
title: Photon OS 3.0 update the maximum failed OS login attempts
author: Harold Preyers
type: post
date: 2023-02-28T18:22:00+00:00
excerpt: 'Quick hack: How to update the maximum failed OS login attempts on Photon OS 3.0'
url: /photon-os-3-0-update-the-maximum-failed-os-login-attempts/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2023/02/uag-pam_tally2.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Photon OS
  - UAG
tags:
  - photonos
  - uag

---
<p class="has-text-align-justify">
  A client of mine was looking on how to update the maximum failed OS login attempts because they were having an issue with the monitoring solution locking the root user account on the VMware Unified Access Gateway 2212. This version of the UAG is based on photon OS 3.0.
</p>

<p class="has-text-align-justify">
  He asked me to verify where to change the configuration to update the maximum failed OS login attempts. This is normally set at UAG deploy time and there is no option to change it afterwards easily.
</p>

<p class="has-text-align-justify">
  Be aware that this is a rather unconventional change because these values shouldn&#8217;t be changed from the default, especially if you want to be compliant with CIS audits for example.
</p>

<p class="has-text-align-justify">
  This is the default and also the line that you need to change
</p>

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " >auth    required        pam_tally2.so file=/var/log/tallylog deny=3 onerr=fail even_deny_root unlock_time=86400 root_unlock_time=300
</pre>
</div>

<p class="has-text-align-justify">
  Open system-auth located in /etc/pam.d
</p>

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " title="Open system-auth" >vi /etc/pam.d/system-auth</pre>
</div>

  * deny=3

<p class="has-text-align-justify">
  Change the deny=3 to the maximum value you want. If you change it to 0 (zero) it will never deny based on the maximum failed OS login attempts for all local users
</p>

  * even\_deny\_root

<p class="has-text-align-justify">
  Leave it out if it shouldn&#8217;t deny the root user being locked out on the maximum failed OS login
</p>

  * unlock_time=86400

<p class="has-text-align-justify">
  Default unlock time for all users
</p>

  * root\_unlock\_time=300

<p class="has-text-align-justify">
  Unlock time for the root user account
</p>

Source: <a href="https://www.stigviewer.com/stig/vmware_vsphere_6.7_photon_os/2022-09-27/" target="_blank" rel="noreferrer noopener">https://www.stigviewer.com/stig/vmware_vsphere_6.7_photon_os/2022-09-27/</a>