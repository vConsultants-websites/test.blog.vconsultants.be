---
title: VCSA 7 U1 available updates error
author: Harold Preyers
type: post
date: 2021-03-12T18:11:22+00:00
excerpt: Update Manager throws an error on a default vCenter 7 U1 install.
url: /vcsa-7-u1-available-updates-error/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/vcenter_upgrade_check_error.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Upgrade
  - vCenter
  - vSphere
tags:
  - upgrade
  - vcenter
  - vcenter 7 U1
  - vCenter 7 U2
  - vsphere

---
<p class="has-text-align-justify">
  Today I deployed a new VCSA 7 U1 and as U2 has GA&#8217;d recently I wanted to update the environment first. So I headed to the VAMI interface > Available Updates page. Immediately there was an error:
</p>

<p class="has-text-color has-background red-message" style="background-color:#f5dddb;color:#575757">
  Error in method invocation ({&#8216;id&#8217;: &#8216;com.vmware.appliance.update.manifest_verification_failed&#8217;, &#8216;default_message&#8217;: &#8216;Manifest verification failed&#8217;, &#8216;args&#8217;: []}, &#8216;Verification Failure\n&#8217;, &#8221;)
</p>



<p class="has-text-align-justify">
  I found some blogs that showed to delete upgrade status file &#8216;software_update_state.conf&#8217; at /etc/applmgmt/appliance. While I tested with renaming this file to .old this did not resolve the error.
</p>

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true ">mv /etc/applmgmt/appliance/software_update_state.conf /etc/applmgmt/appliance/software_update_state.conf.old</pre>
</div>

The file was recreated but held the same info, which is in JSON format and has an the following content:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true ">{
    "state": "UP_TO_DATE"
}</pre>
</div>

<p class="has-text-align-justify">
  &#8220;UP_TO_DATE&#8221;, it clearly is not. So I found this <a href="https://kb.vmware.com/s/article/70837" target="_blank" rel="noreferrer noopener">KB</a> article. This is also where I got the solution for my install. I compared the url I found in the KB article with the one that is included by default in the update settings page.
</p>

The one on the KB page is the following:

https://vapp-updates.vmware.com/vai-catalog/valm/vmw/8d167796-34d5-4899-be0a-6daade4005a3/6.7.0.31000.latest/

The one that is included in the VAMI interface is the following:

https://vapp-updates.vmware.com/vai-catalog/valm/vmw/8d167796-34d5-4899-be0a-6daade4005a3/7.0.1.00200.latest/

In my case when I alleviated the .latest from that url, updates are detected and I can proceed.

So as you can see in the screenshot below (well not entirely but you will need to take my word for it), I selected &#8216;Specified&#8217; and supplied the following url:

https://vapp-updates.vmware.com/vai-catalog/valm/vmw/8d167796-34d5-4899-be0a-6daade4005a3/7.0.1.00200<figure class="wp-block-image size-large">

[<img decoding="async" loading="lazy" width="857" height="420" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/update_settings_link.png?resize=857%2C420&#038;ssl=1" alt="Update settings link" class="wp-image-1772" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/update_settings_link.png?resize=857%2C420&#038;ssl=1 857w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/update_settings_link-480x235.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) 857px, 100vw" data-recalc-dims="1" />][1]</figure> 

As soon I clicked &#8216;SAVE&#8217;, the updates became available to install.<figure class="wp-block-image size-large">

[<img decoding="async" loading="lazy" width="931" height="356" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/available_updates_succes.png?resize=931%2C356&#038;ssl=1" alt="Available updates success" class="wp-image-1773" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/available_updates_succes.png?resize=931%2C356&#038;ssl=1 931w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/available_updates_succes-480x184.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) 931px, 100vw" data-recalc-dims="1" />][2]</figure>

 [1]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/update_settings_link.png?ssl=1
 [2]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/03/available_updates_succes.png?ssl=1