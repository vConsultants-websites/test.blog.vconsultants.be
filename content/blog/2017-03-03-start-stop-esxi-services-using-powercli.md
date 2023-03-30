---
title: Start or stop ESXi services using PowerCLI
author: Harold Preyers
type: post
date: 2017-03-03T06:49:45+00:00
url: /start-stop-esxi-services-using-powercli/
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - PowerCLI
  - VMware
  - vSphere
tags:
  - esxi
  - powercli
  - powercli-snippet
  - ssh
  - vsphere

---
Start the ssh service on all hosts:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps decode:true ">Get-VMHost | Foreach {
	Start-VMHostService -HostService ($_ | Get-VMHostService | Where {$_.Key -eq "TSM-SSH"})
}</pre>
</div>

<div class="wp-block-uagb-info-box uagb-infobox__outer-wrap uagb-block-77fb278d">
  <div class="uagb-infobox__content-wrap uagb-infobox uagb-infobox-has-icon uagb-infobox-icon-left uagb-infobox-left uagb-infobox-image-valign-middle uagb-infobox-enable-border-radius ">
    <div class="uagb-ifb-left-right-wrap">
      <div class="uagb-ifb-image-icon-content uagb-ifb-imgicon-wrap">
        <div class="uagb-ifb-icon-wrap">
          <span class="uagb-ifb-icon"><svg xmlns="http://www.w3.org/2000/svg" viewbox="0 0 512 512"><path d="M104 224H24c-13.255 0-24 10.745-24 24v240c0 13.255 10.745 24 24 24h80c13.255 0 24-10.745 24-24V248c0-13.255-10.745-24-24-24zM64 472c-13.255 0-24-10.745-24-24s10.745-24 24-24 24 10.745 24 24-10.745 24-24 24zM384 81.452c0 42.416-25.97 66.208-33.277 94.548h101.723c33.397 0 59.397 27.746 59.553 58.098.084 17.938-7.546 37.249-19.439 49.197l-.11.11c9.836 23.337 8.237 56.037-9.308 79.469 8.681 25.895-.069 57.704-16.382 74.757 4.298 17.598 2.244 32.575-6.148 44.632C440.202 511.587 389.616 512 346.839 512l-2.845-.001c-48.287-.017-87.806-17.598-119.56-31.725-15.957-7.099-36.821-15.887-52.651-16.178-6.54-.12-11.783-5.457-11.783-11.998v-213.77c0-3.2 1.282-6.271 3.558-8.521 39.614-39.144 56.648-80.587 89.117-113.111 14.804-14.832 20.188-37.236 25.393-58.902C282.515 39.293 291.817 0 312 0c24 0 72 8 72 81.452z"></path></svg></span>
        </div>
      </div>
      
      <div class="uagb-ifb-content">
        <div class="uagb-ifb-title-wrap">
        </div>
        
        <div class="uagb-ifb-text-wrap">
          <p class="uagb-ifb-desc">
            <em>Thanks to Alan Renouf at virtu-al.net, where I found this snippet: <a href="https://www.virtu-al.net/2010/11/23/enabling-esx-ssh-via-powercli/" target="_blank" rel="noreferrer noopener">https://www.virtu-al.net/2010/11/23/enabling-esx-ssh-via-powercli/</a></em>
          </p>
        </div>
      </div>
    </div>
  </div>
</div>

If you want to start the ssh service on a single host, change ESXiHostName to your ESXi FQDN:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps decode:true ">Get-VMHost -Name ESXiHostName | Foreach {
	Start-VMHostService -HostService ($_ | Get-VMHostService | Where {$_.Key -eq "TSM-SSH"})
}</pre>
</div>

If you want to stop the ssh service on all hosts:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps decode:true ">Get-VMHost | Foreach {
	Stop-VMHostService -HostService ($_ | Get-VMHostService | Where {$_.Key -eq "TSM-SSH"})
}</pre>
</div>

If you have multiple cluster in vCenter, are connected to multiple vCenters, be sure to launch the command only to the necessary hosts:

  * Get-Cluster -Name _**ClusterName**_ will filter to the specified Cluster
  * Get-VMHost -Name _**ESXiHostName**_ will filter to the specified ESXi
  * Get-VMHost -Server _**vCenterServerName**_ will filter to the specified vCenter server

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps decode:true ">Get-Cluster -Name ClusterName | Get-VMHost -Name ESXiHostName -Server vCenterServerName | Foreach {
   Stop-VMHostService -HostService ($_ | Get-VMHostService | Where { $_.Key -eq "TSM-SSH"} )
}</pre>
</div>

These are other services I frequently use:

  * DCUI (Direct Console UI)
  * lwsmd (Active Directory Service)
  * ntpd (NTP Daemon)
  * sfcbd-watchdog (CIM Server)
  * snmpd (SNMP Server)
  * TSM (ESXi Shell)
  * TSM-SSH (SSH)
  * vmsyslogd (Syslog Server)
  * vmware-fdm (vSphere High Availability Agent)
  * vpxa (VMware vCenter Agent)
  * xorg (X.Org Server)

There are other services available but I have never used them in this context (yet):

  * lbtd (Load-Based Teaming Daemon)
  * pcscd (PC/SC Smart Card Daemon)
  * vprobed (VProbe Daemon)

Change the startup policy for a service:

  * Automatic: Start automatically if any ports are open, and stop when all ports are closed
  * On: Start and stop with host
  * Off: Start and stop manually

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:ps decode:true ">Get-VMHost | Foreach {
	Set-VMHostService -HostService ($_ | Get-VMHostService | Where {$_.key -eq "TSM-SSH"}) -policy On
}</pre>
</div>