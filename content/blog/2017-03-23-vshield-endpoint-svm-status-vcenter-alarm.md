---
title: vShield Endpoint SVM status vCenter alarm
author: Harold Preyers
type: post
date: 2017-03-23T17:02:07+00:00
url: /vshield-endpoint-svm-status-vcenter-alarm/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield2.jpg
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - ESXi
  - Trend Micro
  - VMware
  - vShield
  - vSphere
tags:
  - esxi
  - trendmicro
  - vshield
  - vsphere

---
vCenter is showing an alarm on the TrendMicro Deep Security Virtual Appliance (DSVA): &#8216;vShield Endpoint SVM status&#8217;<figure class="wp-block-image is-resized">

[<img decoding="async" loading="lazy" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield2.jpg?resize=818%2C177&#038;ssl=1" alt="vShield Endpoint SVM status alarm" class="wp-image-305" width="818" height="177" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield2.jpg?w=694&ssl=1 694w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield2.jpg?resize=300%2C65&ssl=1 300w" sizes="(max-width: 818px) 100vw, 818px" data-recalc-dims="1" />][1]</figure> 

Checking vShield for errors:<figure class="wp-block-image">

[<img decoding="async" loading="lazy" width="1080" height="242" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield.jpg?resize=1080%2C242&#038;ssl=1" alt="" class="wp-image-304" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield.jpg?w=1772&ssl=1 1772w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield.jpg?resize=300%2C67&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield.jpg?resize=768%2C172&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield.jpg?resize=1024%2C229&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield.jpg?resize=1080%2C242&ssl=1 1080w" sizes="(max-width: 1080px) 100vw, 1080px" data-recalc-dims="1" />][2]</figure> 

The DSVA VA console window shows: (as to where it should show a red/grey screen)<figure class="wp-block-image is-resized">

[<img decoding="async" loading="lazy" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/trendmicro.jpg?resize=818%2C451&#038;ssl=1" alt="" class="wp-image-302" width="818" height="451" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/trendmicro.jpg?w=616&ssl=1 616w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/trendmicro.jpg?resize=300%2C166&ssl=1 300w" sizes="(max-width: 818px) 100vw, 818px" data-recalc-dims="1" />][3]</figure> 

Let&#8217;s go for some log file analysis

To get a login prompt: Alt + F2

Login with user dsva and password dsva (this is the default)

The log file we are going to check is the messages log file at /var/log/messages

<pre class="wp-block-code lang:shell"><code>less /var/log/messages</code></pre>

(why less is more: you get almost all the <a href="https://blog.vconsultants.be/usefull-vi-commands/" target="_blank" rel="noreferrer noopener">vi commands</a>)

To go to the last line:

<pre class="wp-block-code lang:shell"><code>G</code></pre><figure class="wp-block-image is-resized">

[<img decoding="async" loading="lazy" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/trendmicro2.jpg?resize=819%2C461&#038;ssl=1" alt="" class="wp-image-303" width="819" height="461" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/trendmicro2.jpg?w=742&ssl=1 742w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/trendmicro2.jpg?resize=300%2C169&ssl=1 300w" sizes="(max-width: 819px) 100vw, 819px" data-recalc-dims="1" />][4]</figure> 

For some reason the ovf file is not like it is expected. The appliance is not able to set some ovf settings, in this case the network interfaces.

To exit the log file display mode:

<pre class="wp-block-code lang:shell"><code>q</code></pre>

To gain root privileges:

<pre class="wp-block-code lang:shell"><code>sudo -s</code></pre>

Enter the dsva user password

Navigate to the /var/opt/ds_agent/slowpath directory

<pre class="wp-block-code lang:shell"><code>cd /var/opt/ds_agent/slowpath</code></pre>

Create the dsva-ovf.env file (if the file exists, delete the existing file first):

<pre class="wp-block-code lang:shell"><code>touch dsva-ovf.env</code></pre>

Reboot the appliance, once rebooted give it 5 minutes and the alarm should clear automatically:

<pre class="wp-block-code lang:shell"><code>reboot</code></pre>

 [1]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield2.jpg?ssl=1
 [2]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/vshield.jpg?ssl=1
 [3]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/trendmicro.jpg?ssl=1
 [4]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2017/03/trendmicro2.jpg?ssl=1