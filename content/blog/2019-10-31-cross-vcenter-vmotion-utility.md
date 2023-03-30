---
title: Cross vCenter vMotion Utility
author: Harold Preyers
type: post
date: 2019-10-31T15:07:39+00:00
excerpt: My take on the Cross vCenter vMotion Utility
url: /cross-vcenter-vmotion-utility/
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Cross vCenter vMotion Utility
  - vCenter
  - VMware
  - vSAN
  - vSphere
tags:
  - homelab
  - vmware

---
<ul class="wp-block-advgb-summary advgb-toc alignnone">
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#step-1-running-the-jar">Step 1 : Running the jar</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#step-2-configuration">Step 2 : Configuration</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#step-3-migration">Step 3 : Migration</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#issues">Issues</a>
  </li>
  <li class="toc-level-2" style="margin-left:40px">
    <a href="#storage-vmotion">Storage vMotion?</a>
  </li>
  <li class="toc-level-2" style="margin-left:40px">
    <a href="#target-datastore-shared-datastore-same-as-source">Target Datastore: Shared datastore (same as source)</a>
  </li>
  <li class="toc-level-1" style="margin-left:20px">
    <a href="#conclusion">Conclusion</a>
  </li>
</ul>

Whilst upgrading the home lab I also decided to rebuild from scratch. There were some challenges to overcome because I have running VMs I don’t want to shut while migrating.

My current home lab setup and the go to setup is documented here (work in progress). Basically it comes down to:

  1. Original setup: three hosts backed with iSCSI storage for running the VMs
  2. Temporary setup:
      1. New vCenter with two of the three hosts configured for vSAN with connection to the iSCSI datastores
      2. Old vCenter with one remaining host running all of the VMs
  3. Destination setup: new vCenter with vSAN datastore

To migrate the virtual machines from the old environment (from the last remaining host to the two new hosts) I decided to take a look at the ‘[Cross vCenter vMotion Utility][1]‘. There is not a lot of documentation available at first sight but it is straightforward to set up and configure. Although I did find some things that are worth noting.

## Step 1 : Running the jar {#step-1-running-the-jar}

To start the Cross vCenter vMotion Utility one must run a jar file: ‘java -jar xvm-2.6.jar’.

I am running linux (Pop!_OS 18.04) as my OS. I have java version 8 and 11 installed with version 11 as default. Version 11 is not listed on the fling site as supported (Java Runtime Environment 1.8-10: See <a href="https://flings.vmware.com/cross-vcenter-workload-migration-utility#requirements" target="_blank" rel="noreferrer noopener">requirements</a>). Running with version 11 (sudo java -jar xvm-2.6.jar) starts the local website on port 8080 (<http://localhost:8080>) but does not report back on the CLI.<figure class="wp-block-image has-lightbox">

<img decoding="async" loading="lazy" width="1851" height="126" src="https://i2.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?w=1080&ssl=1" alt="" class="wp-image-864" title="" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?w=1851&ssl=1 1851w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?resize=300%2C20&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?resize=768%2C52&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?resize=1024%2C70&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?resize=1080%2C74&ssl=1 1080w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?resize=1280%2C87&ssl=1 1280w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?resize=980%2C67&ssl=1 980w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_111.png?resize=480%2C33&ssl=1 480w" sizes="(max-width: 1080px) 100vw, 1080px" /> </figure> 

Under the assumption that the java application started and failed right away, I decided to run it on my windows box which has Java Runtime environment 8 installed. The last line of feedback ‘Initialized controller with empty state’ was the same as on my linux machine. Navigating to [localhost:8080][2]&nbsp;showed the Cross vCenter vMotion Utility web interface. I could now configure the application and run migrations.

It is only later when I closed the running instance on my linux box and restarting it that it showed me output on the CLI that the application started successfully.

<pre class="wp-block-code lang:shell"><code>ps -df | grep -i java
kill -HUP 9159</code></pre><figure class="wp-block-image has-lightbox">

<img decoding="async" loading="lazy" width="1253" height="100" src="https://i2.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_113.png?w=1080&ssl=1" alt="" class="wp-image-865" title="" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_113.png?w=1253&ssl=1 1253w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_113.png?resize=300%2C24&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_113.png?resize=768%2C61&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_113.png?resize=1024%2C82&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_113.png?resize=1080%2C86&ssl=1 1080w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_113.png?resize=980%2C78&ssl=1 980w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_113.png?resize=480%2C38&ssl=1 480w" sizes="(max-width: 1080px) 100vw, 1080px" /> </figure> 

Output after restart:<figure class="wp-block-image has-lightbox">

<img decoding="async" loading="lazy" width="1840" height="229" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?w=1080&ssl=1" alt="" class="wp-image-866" title="" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?w=1840&ssl=1 1840w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?resize=300%2C37&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?resize=768%2C96&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?resize=1024%2C127&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?resize=1080%2C134&ssl=1 1080w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?resize=1280%2C159&ssl=1 1280w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?resize=980%2C122&ssl=1 980w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_114.png?resize=480%2C60&ssl=1 480w" sizes="(max-width: 1080px) 100vw, 1080px" /> </figure> 

## Step 2 : Configuration {#step-2-configuration}

  * Register connections
      1. Source vCenter
      2. Destination vCenter

## Step 3 : Migration {#step-3-migration}

  * Add migrations
      1. Source Site: source vCenter
      2. Target Site: destination vCenter
      3. Source Datacenter
      4. Virtual Machine(s): Select one or more virtual machines
      5. Placement Target: Cluster or Host
      6. Target Datastore
      7. Network Mapping(s): the utility will detect the source networks for all selected virtual machines and display a selection field for the target network

## Issues {#issues}

### Storage vMotion? {#storage-vmotion}

Storage vMotion does not seem to be supported. I tried to svMotion my machines from their iSCSI based datastores to the newly created vSAN datastore but it failed.

### Target Datastore: Shared datastore (same as source) {#target-datastore-shared-datastore-same-as-source}

Choosing ‘Shared datastore (same as source)’ as Target Datastore fails and throws the following error:<figure class="wp-block-image is-resized is-style-default has-lightbox">

<img decoding="async" loading="lazy" src="https://i1.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_110.png?w=1080&ssl=1" alt="" class="wp-image-861" width="769" height="191" title="" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_110.png?w=729&ssl=1 729w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_110.png?resize=300%2C74&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2019/10/Selection_110.png?resize=480%2C119&ssl=1 480w" sizes="(max-width: 769px) 100vw, 769px" /> </figure> 

I added the destination host and tried again but it also failed with several issues:

  * destination networks were not listed, only a subset were – although all were added to the distributed vSwitch
  * matching datastore was not found on the destination host

I could migrate to the new environment but had to select a destination datastore. This posed not much of a problem in my environment because the end goal was to get the virtual machine on the vSAN datastore.

After migrating most of the virtual machines, only two types of virtual machines were left, it felt like I could take a step back if needed. The following types were left to migrate, the vCenter VMs and the firewall VMs. The old vCenter is not needed anymore, the new vCenter and the firewall VMs are and once those are migrated I can go break down the last part of the old setup. The last host will be reset to default settings via the DCUI after which it can be added to the vSAN cluster and I can make the vSAN cluster setup complete. A tmp\_vSAN\_policy with no redundancy is not the way you (or me) want to run your environment, even if it is a lab environment.

## Conclusion {#conclusion}

I could not migrate from the old environment to the new environment while also doing a Storage vMotion, I needed to go in steps.

Nevertheless I’m happy to have used the Cross vCenter vMotion Utility. It did save me a lot of work, required little setup and configuration. I didn’t need to change anything to the setup of my old nor my new environment.

 [1]: https://flings.vmware.com/cross-vcenter-workload-migration-utility
 [2]: http://localhost:8080