---
title: My first deploy with VMware Cloud Foundation
author: Harold Preyers
type: post
toc: true
date: 2019-12-30T15:23:03+00:00
url: /my-first-deploy-with-vmware-cloud-foundation/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2019/11/VCF_Lab_Constructor_beta_v3.8.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - VCF Lab Constructor
  - VLC
  - VMware Cloud Foundation
tags:
  - homelab
  - VCF
  - VLC

---
I have been working on a script to deploy environments on a regular basis in my homelab. While I have made great progress I have not been able to get this completed&nbsp;due to the lack of time. It did up my powershell script writing skills.

A while ago I followed a webinar about&nbsp;<a href="https://t.co/08MOZa92II?amp=1" target="_blank" rel="noreferrer noopener">VMware Cloud Foundation Lab Constructor</a> (VLC in short). This will deploy a VCF environment in a decent amount of time. With little effort I have been able to get this up and running multiple times. There are some pitfalls I ran into.&nbsp;My goal is to get to learn more about VCF, NSX-T and K8s all in a <a href="https://docs.vmware.com/en/VMware-Validated-Design/index.html" target="_blank" rel="noreferrer noopener">VMware Validated Design (VVD)</a> setup.

You can get access too by completing the registration form at&nbsp;<a rel="noreferrer noopener" href="https://t.co/08MOZa92II?amp=1" target="_blank">tiny.cc/getVLC</a>.

The following files are included in the download:

  * Example\_DNS\_Entries.txt
  * VCF Lab Constructor Install Guide 39.pdf
  * VLCGui.ps1
  * add\_3\_hosts.json
  * add\_3\_hosts\_bulk\_commission VSAN.json
  * default-vcf-ems.json
  * default\_mgmt\_hosthw.json
  * maradns-2.0.16-1.x86_64.rpm  
    
  * mkisofs.exe
  * plink.exe

As I already have a DNS infrastructure in place I used ‘Example\_DNS\_Entries.txt‘ as a reference to create all the necessary DNS entries.

Read the **documentation** pdf **FIRST**. It will give you a good insight in what will be set up, won’t be set up and how everything will be set up. I’m not planning to repeat info that is included in the documentation. The only thing that I have copied from this pdf is the disclaimer because I feel it is important:<figure class="wp-block-image">

<img decoding="async" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/12/Selection_117.png?w=1080&ssl=1" alt="" title="" /> </figure> 

Below I have included the various configuration files and split them to show the different parts and also show where I deferred from the default. There are the configuration files that the VLC script will use:

  * Management domain:
      * default-vcf-ems.json&nbsp;→ changed all ip addresses, gateways, hostnames, networks and licenses
      * default\_mgmt\_hosthw.json&nbsp;→ changed the amount of CPUs (8 → 12), the amount of RAM&nbsp;(32 and 64 → 80) and the disk sizes(50,150 and 175 → 150)
  * Workload domain
      * add\_3\_hosts.json&nbsp;→ changed the hostname, management IP and IP gateway

To deploy VCF and be able to deploy NSX-T you will need a good amount of resources. The mimimum of host resources to be able to deploy NSX-T is 12vCPUs (There is a workaround to lower the vCPU requirements for NSX-T) and 80GB of RAM due to the NSX-T requirements.

## The configuration files

The first file is the ‘default\_mgmt\_hosthw.json’. This file describes the specs for the (virtual) hardware for the management domain hosts:

<div class="wp-block-coblocks-gist">
  <noscript>
    <a href="https://gist.github.com/hpreyers/5a4efd583dabc9007bb2b96a42811627">View this gist on GitHub</a>
  </noscript><figcaption>default management host hardware json</figcaption>
</div>

The second file is the ‘default-vcf-ems.json’. This file describes the configuration for all software components for the management domain:

<div class="wp-block-coblocks-gist">
  <noscript>
    <a href="https://gist.github.com/hpreyers/a48b4ea8b6f4a450fe91c5893901e83c">View this gist on GitHub</a>
  </noscript><figcaption>default VCF EMS JSON</figcaption>
</div>

The last configuration file is ‘add\_3\_hosts.json’. This configuration file is optional and can be used to prepare three extra hosts for the first workload domain:

<div class="wp-block-coblocks-gist">
  <noscript>
    <a href="https://gist.github.com/hpreyers/a3b87efd048aab598798f6a5de1efbd6">View this gist on GitHub</a>
  </noscript><figcaption>Add 3 host JSON</figcaption>
</div>

## Where did I change the defaults

There are some settings that I changed from the defaults aside from changing the names and network settings:

  * in the ‘default\_mgmt\_hosthw.json’ I have changed the CPU to 12 to be able to deploy NSX-T
  * in the ‘default\_mgmt\_hosthw.json’ I have changed the RAM 80 to be able to deploy NSX-T

## How do we start

If you are meeting the prerequirements it is fairly simple. Fire up the ‘VLCGui.ps1’. This will present the following gui which will give the ability to supply all the necessary information and to connect to your physical environment. It speaks for itself, just make sure the Cluster, Network Name and Datastore field are higlighted blue like the following.<figure class="wp-block-image">

<img decoding="async" src="https://i2.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/12/Selection_119.png?w=1080&ssl=1" alt="" title="" /> </figure> 

## What’s next

I hope to expand this inital post with a couple of follow-up posts. These are the topics that I’m currently thinking about:

  * NSX-T
  * importing the upgrade and deployment bundles
  * K8s

… and maybe more …

## Additional info

Support:

Slack VLC Support channel –&nbsp;<a href="http://tiny.cc/getVLCSlack" target="_blank" aria-label="undefined (opens in a new tab)" rel="noreferrer noopener">http://tiny.cc/getVLCSlack</a>

Some blogs:

<a href="https://blog.bertello.org/2019/08/building-nested-vcf-using-vcf-lab-constructor-vlc/" target="_blank" aria-label="undefined (opens in a new tab)" rel="noreferrer noopener">https://blog.bertello.org/2019/08/building-nested-vcf-using-vcf-lab-constructor-vlc/</a>&nbsp;and&nbsp;<a href="https://blog.bertello.org/category/automation/" target="_blank" aria-label="undefined (opens in a new tab)" rel="noreferrer noopener">https://blog.bertello.org/category/automation/</a>

<a href="https://my-sddc.net/" target="_blank" aria-label="undefined (opens in a new tab)" rel="noreferrer noopener">https://my-sddc.net/</a>

<a href="https://vinfrastructure.it/2019/10/vmware-cloud-foundation-3-9/" target="_blank" aria-label="undefined (opens in a new tab)" rel="noreferrer noopener">https://vinfrastructure.it/2019/10/vmware-cloud-foundation-3-9/</a>

<a href="https://blogs.vmware.com/cloud-foundation/" target="_blank" aria-label="undefined (opens in a new tab)" rel="noreferrer noopener">https://blogs.vmware.com/cloud-foundation/</a>