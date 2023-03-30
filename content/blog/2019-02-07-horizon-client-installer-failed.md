---
title: Horizon Client Installer Failed
author: Harold Preyers
type: post
date: 2019-02-07T14:11:30+00:00
excerpt: 'Horizon Client Installer Failed #vExpert'
url: /horizon-client-installer-failed/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientFail.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Horizon
  - VMware
tags:
  - horizon
  - linux
  - vmware

---
The Horizon Client installer generates the following errors for Multimedia Redirection:<figure class="wp-block-image">

<img decoding="async" loading="lazy" width="898" height="637" src="https://i2.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientFail.png?w=1080&ssl=1" alt="" class="wp-image-657" title="" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientFail.png?w=898&ssl=1 898w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientFail.png?resize=300%2C213&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientFail.png?resize=768%2C545&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientFail.png?resize=400%2C284&ssl=1 400w" sizes="(max-width: 898px) 100vw, 898px" /> </figure> 

Adding the following symlinks made the failure message go away. I’m wondering though if the packages get updated in the repositories whether this will break the Multimedia Redirection (MMR). I guess I’ll notice some day.

<div class="wp-block-coblocks-gist">
  <noscript>
    <a href="https://gist.github.com/hpreyers/ae3084800591e592e16f854bf6878dec">View this gist on GitHub</a>
  </noscript>
</div>

**Update (2019/12/20):** Today I updated from version 5.2 to 5.3 and ran into the same issue again. I noticed that there are symbolic links present but that they linked to the old versions. After updating the symbolic links the installer was happy again.<figure class="wp-block-image">

<img decoding="async" loading="lazy" width="898" height="637" src="https://i2.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientSuccess.png?w=1080&ssl=1" alt="" class="wp-image-659" title="" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientSuccess.png?w=898&ssl=1 898w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientSuccess.png?resize=300%2C213&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientSuccess.png?resize=768%2C545&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/02/HorizonClientSuccess.png?resize=400%2C284&ssl=1 400w" sizes="(max-width: 898px) 100vw, 898px" /> </figure> 

Through [https://communities.vmware.com][1] I found  
<https://www.werts.nl/vmware-view-horizon-client-for-linux-on-ubuntu-18-04-failed/>

 [1]: https://communities.vmware.com/thread/587792