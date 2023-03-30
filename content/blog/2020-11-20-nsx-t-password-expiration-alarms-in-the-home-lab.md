---
title: NSX-T password expiration alarms in the Home Lab
author: Harold Preyers
type: post
date: 2020-11-20T13:42:59+00:00
url: /nsx-t-password-expiration-alarms-in-the-home-lab/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/11/image.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - home lab
  - NSX-T
  - VMware
tags:
  - homelab
  - nsx-t

---
## The challenge

I have a couple of NSX-T environments in my home lab. I logged on to one of them and saw a couple of open NSX-T password expiration alarms.<figure class="wp-block-image size-large has-lightbox">

<img decoding="async" loading="lazy" width="1024" height="84" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/11/image.png?resize=1024%2C84&#038;ssl=1" alt="Password expiration alarms" class="wp-image-1397" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/11/image-980x80.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/11/image-480x39.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /> </figure> 

<div class="is-layout-flow wp-block-group red-message">
  <div class="wp-block-uagb-info-box uagb-infobox__outer-wrap uagb-block-2f7c2cda">
    <div class="uagb-infobox__content-wrap uagb-infobox uagb-infobox-has-icon uagb-infobox-icon-left uagb-infobox-left uagb-infobox-stacked-tablet uagb-infobox-image-valign-middle uagb-infobox-enable-border-radius ">
      <div class="uagb-ifb-left-right-wrap">
        <div class="uagb-ifb-image-icon-content uagb-ifb-imgicon-wrap">
          <div class="uagb-ifb-icon-wrap">
            <span class="uagb-ifb-icon"><svg xmlns="http://www.w3.org/2000/svg" viewbox="0 0 512 512"><path d="M504 256c0 136.997-111.043 248-248 248S8 392.997 8 256C8 119.083 119.043 8 256 8s248 111.083 248 248zm-248 50c-25.405 0-46 20.595-46 46s20.595 46 46 46 46-20.595 46-46-20.595-46-46-46zm-43.673-165.346l7.418 136c.347 6.364 5.609 11.346 11.982 11.346h48.546c6.373 0 11.635-4.982 11.982-11.346l7.418-136c.375-6.874-5.098-12.654-11.982-12.654h-63.383c-6.884 0-12.356 5.78-11.981 12.654z"></path></svg></span>
          </div>
        </div>
        
        <div class="uagb-ifb-content">
          <div class="uagb-ifb-title-wrap">
            <h3 class="uagb-ifb-title">
              CAUTION
            </h3>
          </div>
          
          <div class="uagb-ifb-text-wrap">
            <p class="uagb-ifb-desc">
              Password expiration should be part of your password policy strategy. Disabling the password expiration on a production system is not a good strategy.
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

## The solution

With my sharp googling skills, I found this reference in the NSX-T 3.0 docs:

<https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.0/installation/GUID-89E9BD91-6FD4-481A-A76F-7A20DB5B916C.html>

So I changed the admin password &#8216;password-expiration&#8217;, not even bothering to open the event details. I just assumed this is about the admin user.

<pre class="wp-block-code lang:shell"><code>clear user admin password-expiration</code></pre>

Done.

Not true. Some time later that day I found that the alarms were still open. I figured that this is some sort of timing issue, that the alarms were not automatically cleared yet. So I set them to resolved manually. Almost the same minute the alarms are triggered again, so no timing issue. If I only would have counted the alarms the first time it would have showed me that there more alarms than NSX-T components where I cleared the password expiration for the admin user.<figure class="wp-block-image size-large has-lightbox">

<img decoding="async" loading="lazy" width="1024" height="118" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/11/image-1.png?resize=1024%2C118&#038;ssl=1" alt="Password expiration, read the details" class="wp-image-1399" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/11/image-1-980x113.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/11/image-1-480x55.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /> </figure> 

It was only when I read the alarm in detail that I noticed the alarm is not the same one I saw before. This alarm was not triggered about the password expiration of the admin user but showed that it was for the audit user. The alarms are very much the same only the username is different, so easily overlooked.

So doing the math. Initially I had 8 open alarms, of which 3 were put to resolved automatically after changing the password expiration of the admin user. One on the NSX-T Manager and one on each of the 2 edge nodes. Which left 5 open alarms to take care of. Checking all the alarms gave me the following actions:

  * clear alarm for the root user on NSX-T Manager
  * clear alarms for the root user and the audit user on the NSX-T Edge 1 and 2

<div class="is-layout-flow wp-block-group red-message">
  <div class="wp-block-uagb-info-box uagb-infobox__outer-wrap uagb-block-9b4db83b">
    <div class="uagb-infobox__content-wrap uagb-infobox uagb-infobox-has-icon uagb-infobox-icon-left uagb-infobox-left uagb-infobox-stacked-tablet uagb-infobox-image-valign-middle uagb-infobox-enable-border-radius ">
      <div class="uagb-ifb-left-right-wrap">
        <div class="uagb-ifb-image-icon-content uagb-ifb-imgicon-wrap">
          <div class="uagb-ifb-icon-wrap">
            <span class="uagb-ifb-icon"><svg xmlns="http://www.w3.org/2000/svg" viewbox="0 0 512 512"><path d="M504 256c0 136.997-111.043 248-248 248S8 392.997 8 256C8 119.083 119.043 8 256 8s248 111.083 248 248zm-248 50c-25.405 0-46 20.595-46 46s20.595 46 46 46 46-20.595 46-46-20.595-46-46-46zm-43.673-165.346l7.418 136c.347 6.364 5.609 11.346 11.982 11.346h48.546c6.373 0 11.635-4.982 11.982-11.346l7.418-136c.375-6.874-5.098-12.654-11.982-12.654h-63.383c-6.884 0-12.356 5.78-11.981 12.654z"></path></svg></span>
          </div>
        </div>
        
        <div class="uagb-ifb-content">
          <div class="uagb-ifb-title-wrap">
            <h3 class="uagb-ifb-title">
              CAUTION
            </h3>
          </div>
          
          <div class="uagb-ifb-text-wrap">
            <p class="uagb-ifb-desc">
              Password expiration should be part of your password policy strategy. Disabling the password expiration on a production system is not a good strategy.
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>