---
title: Unsupported upgrade of VCSA 6.5 U2 to 6.7
author: Harold Preyers
type: post
date: 2018-06-28T15:52:29+00:00
url: /unsupported-upgrade-of-vcsa-6-5-u2-to-6-7/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/06/header.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Upgrade
  - vCenter
  - VMware
  - vSphere
tags:
  - upgrade
  - vcsa
  - vmware

---
# 

I decided to upgrade the vCenter Server Appliance from 6.5 U2 to 6.7 though it is not supported, see <a href="https://kb.vmware.com/s/article/53704" target="_blank" rel="noreferrer noopener">VMware KB</a>. As this is not supported you will **NOT** want go ahead with this in a production environment. Maybe I will have regrets later on too … but this is my lab environment so the alternative is to redeploy a new VCSA.<figure class="wp-block-image">

<img decoding="async" loading="lazy" width="949" height="275" src="https://i1.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2018/06/header.png?fit=949%2C275&ssl=1" alt="" class="wp-image-462" title="header" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2018/06/header.png?w=949&ssl=1 949w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2018/06/header.png?resize=300%2C87&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2018/06/header.png?resize=768%2C223&ssl=1 768w" sizes="(max-width: 949px) 100vw, 949px" /> </figure> 

<div class="wp-block-coblocks-alert is-style-success" style="background-color:;color:">
  <p class="wp-block-coblocks-alert__title">
    <strong>Update:</strong>
  </p>
  
  <p class="wp-block-coblocks-alert__text">
    While it was not possible at the time of writing of this post. This VMware KB has been updated with the following information.<br /><br /><strong>Important: </strong><br />vSphere 6.5 Update 2d and higher are not supported to upgrade to vSphere 6.7 Update 1.<br />vSphere 6.5 Update 2d and higher are supported to upgrade to vSphere 6.7 Update 2. For more information, see <a href="https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#upgrade&solution=2" target="_blank" rel="noreferrer noopener">VMware Product Interoperability Matrices</a>.
  </p>
</div>

<p class="has-custom-lineheight" style="line-height:2">
  <br />I have applied the following knowledge base articles on the source VCSA:
</p>

  * <https://kb.vmware.com/s/article/2113947>
  * <https://kb.vmware.com/s/article/54724>

The first KB was applied because the installer is failing due to a lack of disk space on the source appliance. The installer gives the opportunity to supply a location on the source VCSA to export the necessary files that facilitate the upgrade.

The second KB was applied because the VMware Directory failed during the firstboot phase after the upgrade succeeded.

I downloaded the sources for VCSA 6.7.0 but had to go and download the sources for VCSA 6.7.0a. The VCSA 6.7.0 sources stalled at 5% on&nbsp;VMware Identity Management Service.

I also went to change the root password expiration to no and set the <administrator@vsphere.local>&nbsp;account password to only include alphabet characters.

The installer will also fail after the first phase if the VAMI port is not reachable, the first phase will finish succesfully though. I forgot to add an exception to my firewall. You can then continue the installer by going to the VAMI interface on port 5480.