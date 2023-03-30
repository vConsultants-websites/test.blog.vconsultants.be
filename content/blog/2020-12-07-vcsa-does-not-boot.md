---
title: VCSA does not boot due to file system errors
author: Harold Preyers
type: post
date: 2020-12-07T15:52:28+00:00
excerpt: vCSA does not boot due to issues with a LVM physical volume
url: /vcsa-does-not-boot/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/issue_screen-e1637174890119.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - home lab
  - vCenter
  - VMware
tags:
  - fsck
  - homelab
  - lvm
  - vcsa

---
 

<ul class="wp-block-advgb-summary advgb-toc alignnone">
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#where-do-i-start-troubleshooting-e3e75a08-15c4-4e29-8b86-196a2b12f6e0">Where do I start troubleshooting?</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#journalctl-xb-a0656c19-f7c9-4290-86f5-a968e838acad">journalctl -xb</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#file-system-check-faca00af-7f59-4980-96f7-48e5f570648d">File System Check</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#other-volumes-c935d625-c30f-4312-a901-1d6c3c78a0f7">Other volumes</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#reboot-ebe40950-821d-4f02-bbd7-7841383e4ebe">Reboot</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#remark-cda68fef-68e2-4557-b868-0009c4583864">Remark</a>
  </li>
</ul>

Due to a power failure of the storage where the vCenter Server Appliance resides, the VCSA does not boot. Connecting to the console shows the following output:<figure class="wp-block-image size-large">

<img decoding="async" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/issue_screen.png?w=1080&#038;ssl=1" alt="Failed to check /dev/log_vg/log" class="wp-image-1738" data-recalc-dims="1" /> </figure> 

When you see this screen, none of the services are started as the appliance does not fully start. This implies that there is no means of connecting to the H5 client nor the VAMI interface on port 5480.

## Why does the VCSA not boot and where do I start troubleshooting? {#where-do-i-start-troubleshooting-e3e75a08-15c4-4e29-8b86-196a2b12f6e0}

There are two important things to mentioned on the screenshot above, this is where we start:

  * Failed to start File System Check on /dev/log_vg/log
  * journalctl -xb

First we take a look at &#8216;journalctl -xb&#8217;. To do this we need to supply the root password and launch the BASH:<figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="800" height="245" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/launch_bash.png?resize=800%2C245&#038;ssl=1" alt="launch BASH" class="wp-image-1739" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/launch_bash.png?resize=800%2C245&#038;ssl=1 800w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/launch_bash-480x147.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) 800px, 100vw" data-recalc-dims="1" /> </figure> 

Now that have shell access we can take a look at &#8216;journalctl -xb&#8217;:

<pre class="wp-block-code lang:shell"><code>journalctl -xb</code></pre>

Type G to go to the bottom of the log file:

<pre class="wp-block-code lang:shell"><code>G</code></pre>

## journalctl -xb {#journalctl-xb-a0656c19-f7c9-4290-86f5-a968e838acad}

Work upwards, the most relevant logs will be at the bottom. For the sake of this blog post, I have type -S. This will turn on/off word wrap, in this case, I turned on word wrap.

## File System Check {#file-system-check-faca00af-7f59-4980-96f7-48e5f570648d}

Going up a little I find these entries:<figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="815" height="552" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/journalctl_entry.png?resize=815%2C552&#038;ssl=1" alt="journalctl showing more info about the failed volume" class="wp-image-1741" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/journalctl_entry.png?resize=815%2C552&#038;ssl=1 815w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/journalctl_entry-480x325.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) 815px, 100vw" data-recalc-dims="1" /> </figure> 

There is a problem with a certain inode and File System Check (fsck) should be run. Let&#8217;s see how we can do that. Is it as simple as running:

<pre class="wp-block-code lang:shell"><code>fsck /dev/mapper/log_vg-log</code></pre>

It seems like it. Running the above command finds some errors and suggests to repair. I confirmed.

## Other volumes {#other-volumes-c935d625-c30f-4312-a901-1d6c3c78a0f7}

Let&#8217;s check the other logical volumes (lvm). First we will run &#8216;lsblk&#8217; to take a look at the drive layout:

<pre class="wp-block-code lang:shell"><code>lsblk</code></pre><figure class="wp-block-image size-large is-resized">

<img decoding="async" loading="lazy" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/lsblk.png?resize=815%2C685&#038;ssl=1" alt="With lsblk we take a look at the drive layout" class="wp-image-1742" width="815" height="685" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/lsblk.png?resize=815%2C685&#038;ssl=1 815w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/lsblk-480x404.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) 815px, 100vw" data-recalc-dims="1" /> </figure> 

_<span style="text-decoration: underline;">Remark</span>_: When we take a look at the type, we see the disks, eg. sda, sdb, etc&#8230; The difference between sda and the rest is that sda is partitioned with standard partitions and on the rest the disks an LVM has been created. 

I checked all other volumes and found none of them were having issues.

## Reboot {#reboot-ebe40950-821d-4f02-bbd7-7841383e4ebe}

To reboot while you are in maintenance boot:

<pre class="wp-block-code"><code>reboot --force</code></pre>

After the reboot, I could connect to the H5 client and clear the relevant errors.

## Remark {#remark-cda68fef-68e2-4557-b868-0009c4583864}

This blog post is very similar to this one [here][1]. Although they are very much alike, the issues in the older blog post were on a standard partition on a VCSA 6.5 whereas the issues described and addressed in this post are on a VCSA 7.0 LVM physical volume.

 [1]: https://blog.vconsultants.be/370-2/