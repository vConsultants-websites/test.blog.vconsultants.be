---
title: Use iPerf to test NIC speed between two ESXi hosts
author: Harold Preyers
type: post
date: 2020-10-26T11:23:14+00:00
url: /use-iperf-to-test-nic-speed-between-to-esxi-hosts/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/06/2020-06-12_09-43.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - ESXi
  - VMware
  - vSphere
tags:
  - esxi
  - vmware
  - vsphere

---
Sometimes you want/need use iPerf to test the nic speed between two ESXi hosts. I did because I was seeing a NIC with low throughput in my lab.

How can we test raw speeds between the two hosts? iPerf comes to the rescue. I was looking on how to do this on an ESXi host. I doesn&#8217;t come as a surprise that I found the solution <a rel="noreferrer noopener" href="https://www.virtuallyghetto.com/2016/03/quick-tip-iperf-now-available-on-esxi.html" target="_blank">here</a> at William Lams&#8217; virtuallyghetto.com. Apparently iperf has been added to ESXi since 6.5 U2. You used to have to copy iperf to iperf.copy. In ESXi 7.0 that has been done for you, although you will need to look for /usr/lib/vmware/vsan/bin/iperf3.copy

#### ESXi host 1 (iperf server) {#esxi-host-1-iperf-server-4cb61287-d2af-4439-a7d2-84cef50b38c0}

Disable the firewall:

<pre class="wp-block-code lang:shell"><code>esxcli network firewall set --enabled false</code></pre>

Change to the directory containing the iperf binary

<pre class="wp-block-code lang:shell"><code>cd /usr/lib/vmware/vsan/bin/</code></pre>

Execute iPerf as server

<pre class="wp-block-code lang:shell"><code>./iperf3.copy -s -B 10.11.6.171</code></pre>

Overview of the used parameters:

<table class="wp-block-advgb-table advgb-table-frontend is-style-default">
  <tr>
    <td style="border-style:none;text-align:center">
      -s
    </td>
    
    <td style="padding-top:0;border-width:0;border-top-color:#000;border-right-color:#000;border-bottom-color:#000;border-left-color:#000;border-style:none;vertical-align:bottom">
      will start iperf as server
    </td>
  </tr>
  
  <tr>
    <td style="vertical-align:middle;border-style:none;text-align:center">
      -B
    </td>
    
    <td style="vertical-align:middle;border-style:none">
      defines the IP the iperf server will listen to
    </td>
  </tr>
</table>

Disable the firewall

<pre class="wp-block-code lang:shell"><code>esxcli network firewall set --enabled false</code></pre>

#### ESXi host 2 (iperf client) {#esxi-host-2-iperf-client-4dfd53a5-c9f8-41d0-be58-e056ad411a6a}

Change to the directory containing the iperf binary

<pre class="wp-block-code lang:shell"><code>cd /usr/lib/vmware/vsan/bin/</code></pre>

Execute iPerf as client

<pre class="wp-block-code lang:shell"><code>./iperf3.copy -i 1 -t 10 -c 10.11.6.171 -fm</code></pre>

Overview of the used parameters:

<table class="wp-block-advgb-table advgb-table-frontend is-style-default">
  <tr>
    <td style="border-style:none;text-align:center">
      -i
    </td>
    
    <td style="padding-top:0;border-width:0;border-top-color:#000;border-right-color:#000;border-bottom-color:#000;border-left-color:#000;border-style:none;vertical-align:bottom">
      will determine the interval of reporting back
    </td>
  </tr>
  
  <tr>
    <td style="border-style:none;text-align:center">
      -t
    </td>
    
    <td style="padding-top:0;border-width:0;border-top-color:#000;border-right-color:#000;border-bottom-color:#000;border-left-color:#000;border-style:none;vertical-align:bottom">
      time iperf will be running
    </td>
  </tr>
  
  <tr>
    <td style="vertical-align:middle;border-style:none;text-align:center">
      -c
    </td>
    
    <td style="vertical-align:middle;border-style:none">
      client ip, will force the usage of the correct vmkernel interface
    </td>
  </tr>
  
  <tr>
    <td style="vertical-align:middle;border-style:none;text-align:center">
      -fm
    </td>
    
    <td style="vertical-align:middle;border-style:none">
      defaults to kbit/s, adding m will use mbit/s
    </td>
  </tr>
</table>

<div class="wp-block-uagb-info-box uagb-infobox__outer-wrap uagb-block-5e2d1686">
  <div class="uagb-infobox__content-wrap uagb-infobox uagb-infobox-has-icon uagb-infobox-icon-left uagb-infobox-left uagb-infobox-stacked-tablet uagb-infobox-image-valign-middle uagb-infobox-enable-border-radius ">
    <div class="uagb-ifb-left-right-wrap">
      <div class="uagb-ifb-image-icon-content uagb-ifb-imgicon-wrap">
        <div class="uagb-ifb-icon-wrap">
          <span class="uagb-ifb-icon"><svg xmlns="http://www.w3.org/2000/svg" viewbox="0 0 512 512"><path d="M504 256c0 136.997-111.043 248-248 248S8 392.997 8 256C8 119.083 119.043 8 256 8s248 111.083 248 248zm-248 50c-25.405 0-46 20.595-46 46s20.595 46 46 46 46-20.595 46-46-20.595-46-46-46zm-43.673-165.346l7.418 136c.347 6.364 5.609 11.346 11.982 11.346h48.546c6.373 0 11.635-4.982 11.982-11.346l7.418-136c.375-6.874-5.098-12.654-11.982-12.654h-63.383c-6.884 0-12.356 5.78-11.981 12.654z"></path></svg></span>
        </div>
      </div>
      
      <div class="uagb-ifb-content">
        <div class="uagb-ifb-title-wrap">
          <h4 class="uagb-ifb-title">
            Don&#8217;t forget to re-enable the firewall on both systems.
          </h4>
        </div>
        
        <div class="uagb-ifb-separator-parent">
          <div class="uagb-ifb-separator">
          </div>
        </div>
        
        <div class="uagb-ifb-text-wrap">
          <p class="uagb-ifb-desc">
            <span class="crayon-inline lang:sh decode:true">esxcli network firewall set &#8211;enabled true</span>
          </p>
        </div>
      </div>
    </div>
  </div>
</div>