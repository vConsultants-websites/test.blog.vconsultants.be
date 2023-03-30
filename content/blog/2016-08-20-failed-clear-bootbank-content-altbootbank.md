---
title: 'Failed to clear bootbank content /altbootbank: [Errno 9] Bad file descriptor: ‘/altbootbank/state.xxxxxxx’'
author: Harold Preyers
type: post
date: 2016-08-20T21:57:21+00:00
url: /failed-clear-bootbank-content-altbootbank/
vantage_panels_no_legacy:
  - 'true'
siteorigin_page_settings:
  - 'a:6:{s:6:"layout";s:7:"default";s:10:"page_title";b:1;s:15:"masthead_margin";b:1;s:13:"footer_margin";b:1;s:13:"hide_masthead";b:0;s:19:"hide_footer_widgets";b:0;}'
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - ESXi
  - VMware
  - vSphere
tags:
  - esxi
  - vsphere

---
In a VSAN project the VMware Compatibility Guide mentioned a different driver version for the raid controller than the one that was installed. So&nbsp;I tried to install a driver update for the&nbsp;raid controller through the CLI. This did not work out as expected&nbsp;because the /altbootbank was in a corrupted state. There were two ways to go ahead, either reinstall from scratch or try to rebuild the /altbootbank from the /bootbank contents. This was not a production server so I had the freedom to apply a more experimental approach and therefor I chose the not supported, not recommended approach to rebuild the /altbootbank from the /bootbank contents.

I ran the following command to install the driver:

<pre class="wp-block-code lang:shell"><code>esxcli software vib install -d /vmfs/volumes/datastore/patch.zip</code></pre>

I got the following error message:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="nums:false nums-toggle:false lang:sh decode:true " >[InstallationError]
Failed to clear bootbank content /altbootbank: [Errno 9] Bad file descriptor: '/altbootbank/state.xxxxxxx'

Please refer to the log file for more details.</pre>
</div>

<span style="color: #000000; font-family: Calibri;">I found the following two links describing the issue</span>:

<https://communities.vmware.com/thread/413441?start=0&tstart=0>

<https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2033564>

The vmware KB&nbsp;is going through the&nbsp;steps&nbsp;to solve this, which in this case didn’t. The better solution is to repair or reinstall but this is a time consuming task.

The steps in the KB didn&#8217;t solve it, so I tried to delete it with<span style="color: #000000; font-family: Calibri;">:</span>

<pre class="wp-block-preformatted lang:shell">rm /altbootbank/state.5824665/</pre>

<pre class="wp-block-preformatted lang:shell">rm –rf /altbootbank/state.5824665/</pre>

The ghost file/directory would not&nbsp;delete. The first command returned &#8216;This is not a file&#8217;, the second &#8216;This is not a directory&#8217;.  
I repeated the same commands after a reboot with the same results. As the server was still booting well I knew the /bootbank was still ok. I wanted to replace the /altbootbank with the contents of the /bootbank partition.

<div class="wp-block-uagb-info-box uagb-infobox__outer-wrap uagb-block-bc4694d8">
  <div class="uagb-infobox__content-wrap uagb-infobox uagb-infobox-has-icon uagb-infobox-icon-left uagb-infobox-left uagb-infobox-stacked-tablet uagb-infobox-image-valign-middle uagb-infobox-enable-border-radius ">
    <div class="uagb-ifb-left-right-wrap">
      <div class="uagb-ifb-image-icon-content uagb-ifb-imgicon-wrap">
        <div class="uagb-ifb-icon-wrap">
          <span class="uagb-ifb-icon"><svg xmlns="http://www.w3.org/2000/svg" viewbox="0 0 512 512"><path d="M504 256c0 136.997-111.043 248-248 248S8 392.997 8 256C8 119.083 119.043 8 256 8s248 111.083 248 248zm-248 50c-25.405 0-46 20.595-46 46s20.595 46 46 46 46-20.595 46-46-20.595-46-46-46zm-43.673-165.346l7.418 136c.347 6.364 5.609 11.346 11.982 11.346h48.546c6.373 0 11.635-4.982 11.982-11.346l7.418-136c.375-6.874-5.098-12.654-11.982-12.654h-63.383c-6.884 0-12.356 5.78-11.981 12.654z"></path></svg></span>
        </div>
      </div>
      
      <div class="uagb-ifb-content">
        <div class="uagb-ifb-title-wrap">
        </div>
        
        <div class="uagb-ifb-text-wrap">
          <p class="uagb-ifb-desc">
            <strong>THE FOLLOWING IS NOT RECOMMENDED NOR SUPPORTED! DO NOT EXECUTE ON A PRODUCTION ENVIRONMENT !</strong>
          </p>
        </div>
      </div>
    </div>
  </div>
</div>

Identity the naaID and partition number of the /altbootbank:

<pre class="wp-block-preformatted lang:shell">vmkfstools -Ph /altbootbank</pre>

Scratch the partition through recreating the file system:

<pre class="wp-block-preformatted lang:shell">vmkfstools -C vfat /dev/disks/naaID:partitionNumber</pre>

Remove the /altbootbank folder:

<pre class="wp-block-preformatted lang:shell">rm –rf /altbootbank</pre>

Create a symlink to the newly created vFat volume with /altbootbank:

<pre class="wp-block-preformatted lang:shell">ln –s /vmfs/volumes/volumeGUID /altbootbank</pre>

Copy all the contents from /bootbank to /altbootbank:

<pre class="wp-block-preformatted lang:shell">cp /bootbank/* /altbootbank</pre>

Change the bootstate=3 in /altbootbank/boot.cfg

<pre class="wp-block-preformatted lang:shell">vi /altbootbank/boot.cfg</pre>

Run /sbin/autobackup.sh script to update the changes

<pre class="wp-block-preformatted lang:shell">/sbin/autobackup.sh</pre>