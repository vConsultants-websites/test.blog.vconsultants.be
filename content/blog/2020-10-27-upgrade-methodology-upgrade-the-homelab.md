---
title: Upgrade Methodology – Upgrade the homelab
author: Harold Preyers
type: post
date: 2020-10-27T08:00:00+00:00
url: /upgrade-methodology-upgrade-the-homelab/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/10/upgrade_sequence.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - ESXi
  - home lab
  - Upgrade
  - vCenter
  - VMware
  - vSAN
  - vSphere
tags:
  - methodology
  - upgrade
  - vsphere

---
This post is not as a end-to-end upgrade guide but a methodology guide. Everything is more or less straight forward if one uses the correct methodology. 

As this is a home lab I have chosen not to add complexity by adding additional nodes to the various components for High Availability but depend on vSphere HA. This eases and shortens my upgrade path. In a production environment every application should be evaluated and talked through with the stakeholders whether it can rely on vSphere HA or some form of application HA should be introduced.

The release of vSphere 7 introduced a starting point for an update/upgrade round in the lab. I have not always used the methodology when upgrading components as I should have in the lab. When I used this methodology in the current upgrade round it has come to light that some components were not interoperable with each other. Why is this important? If you have a problem with your environment and you call VMware support, they will go through the logs and verify the environment is on par with the documentation.

So where do I start?

Reading the docs first takes time but it will save a lot of time in the long run. Can&#8217;t stress it enough RTFM !!

I always create my own high level upgrade guide. This will include the research that I have done and it also includes the upgrade path to follow.

## Phase 1: Information gathering

### Determine the components and versions {#BOM}

Be thorough in determining the components and versions (BOM or **B**ill **O**f **M**aterials). 

  * VMware vSphere 6.7U3
  * vRealize Log Insight 4.5
  * vRealize Operations 7.5
  * VMware Horizon 7.11

You will see below that I had forgotten to include VMware Horizon in my component set. This could have been catastrophic as some components might stop to work when you don&#8217;t follow the correct upgrade procedure.

### Gather the documentation {#Documentation}

After you determine which components are used in the environment you can go and search for the necessary documentation. Find the release notes, upgrade guides and other relevant documentation

I used the following documentation set:

#### Release notes:

  * <a rel="noreferrer noopener" href="https://docs.vmware.com/en/vRealize-Log-Insight/8.1/rn/vRealize-Log-Insight-81.html" target="_blank">vRealize Log Insig</a><a rel="noreferrer noopener" href="https://docs.vmware.com/en/vRealize-Log-Insight/8.1/rn/vRealize-Log-Insight-81.html" target="_blank">h</a><a rel="noreferrer noopener" href="https://docs.vmware.com/en/vRealize-Log-Insight/8.1/rn/vRealize-Log-Insight-81.html" target="_blank">t 8.1</a>
  * <a rel="noreferrer noopener" href="https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/rn/vRealize-Operations-Manager-81.html" target="_blank">vRealize Operations Manager 8.1</a>
  * <a rel="noreferrer noopener" href="https://docs.vmware.com/en/VMware-vSphere/7.0/rn/vsphere-esxi-vcenter-server-70-release-notes.html" target="_blank">vSphere 7.0</a>

#### Upgrade documentation:

  * vRealize Log Insight 8.1
      * <a rel="noreferrer noopener" href="https://docs.vmware.com/en/vRealize-Log-Insight/8.1/com.vmware.log-insight.administration.doc/GUID-B24ECC5C-16D5-4E29-BBE2-EA09E80278CA.html" target="_blank">vRealize Log Insight 8.1 &#8211; Upgrading vRealize Log Insight</a>
  * vRealize Operations Manager 8.1
      * <a rel="noreferrer noopener" href="https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/rn/vRealize-Operations-Manager-81.html" target="_blank">vRealize Operations Manager 8.1 &#8211; Upgrade, Backup and Restore</a> 
  * vSphere 7.0
      * <a rel="noreferrer noopener" href="https://pathfinder.vmware.com/path/accelerate_digital_transformation_by_upgrading_your_vmware_vsphere_environment?int_cid=7012H0000021RY8&src=WWW_us_VMW_2bWCsStKYxPMklLfdjy8" target="_blank">Accelerate VMware vSphere Upgrades</a>
          * vSphere Assessment Tool → accessible through <a rel="noreferrer noopener" href="https://pathfinder.vmware.com/path/accelerate_digital_transformation_by_upgrading_your_vmware_vsphere_environment?int_cid=7012H0000021RY8&src=WWW_us_VMW_2bWCsStKYxPMklLfdjy8" target="_blank">Accelerate VMware vSphere Upgrades</a>
          * eBook on upgrading to vSphere 6.7→ accessible through <a rel="noreferrer noopener" href="https://pathfinder.vmware.com/path/accelerate_digital_transformation_by_upgrading_your_vmware_vsphere_environment?int_cid=7012H0000021RY8&src=WWW_us_VMW_2bWCsStKYxPMklLfdjy8" target="_blank">Accelerate VMware vSphere Upgrades</a>
      * <a rel="noreferrer noopener" href="https://kb.vmware.com/s/article/78205" target="_blank">vSphere 7 Upgrade Best Practices</a>
      * <a rel="noreferrer noopener" href="https://kb.vmware.com/s/article/78487?lang=en_US" target="_blank">Important information before upgrading to vSphere 7.0</a>

Other:

  * <a rel="noreferrer noopener" href="https://kb.vmware.com/s/article/78221" target="_blank">Update sequence for vSphere 7.0 and its compatible VMware products</a>
  * <a rel="noreferrer noopener" href="https://www.vmware.com/resources/compatibility/search.php" target="_blank">VMware Hardware Compatibility List</a>
  * <a rel="noreferrer noopener" href="https://www.vmware.com/resources/compatibility/sim/interop_matrix.php" target="_blank">VMware Product Interoperability Matrices</a>

#### Remarks

In the &#8216;Compatibility Considerations&#8217; in <a rel="noreferrer noopener" href="https://kb.vmware.com/s/article/78487?lang=en_US" target="_blank">Important information before upgrading to vSphere 7.0</a> vRealize Operations is mentioned as not compatible with vSphere 7.0. This will be overruled by the VMware Product Interoperability Matrix between the two products ([VMware Product Interoperability Matrix overview][1] section),

### VMware Hardware Compatibility List {#HCL}

What I learned from these documents was that I was not sure the NVMe drives in my hosts would be compatible. After all they are consumer grade NVMe drives and are not on the VMware HCL.  
I recently installed two NVMe drives, a CT500P1SSD8 and a CT1000P1SSD8 and at the time of install the Crucial CT500P1SSD8 was not recognized. A quick googling showed me a <a rel="noreferrer noopener" href="https://www.virtuallyghetto.com/2019/05/quick-tip-crucial-nvme-ssd-not-recognized-by-esxi-6-7.html" target="_blank">blog post</a> from <a rel="noreferrer noopener" href="https://twitter.com/lamw" target="_blank">William Lam</a> that replaced the vSphere 6.7 U3 nvme driver with one from a vSphere 6.5U2 install.  
I will discuss how I determined if there would be issues around this in ESXi 7.0 in a later phase.

All components should be checked with the vendor and the VMware HCL. Be aware that the vendor and VMware might not always agree and that the VMware HCL might not always be in sync with the the documentation of the hardware vendor. You should always follow the VMware HCL but be aware of the following <a rel="noreferrer noopener" href="http://kb.vmware.com/kb/2030818" target="_blank">KB</a>. If vSAN hardware is involved it is advised to use extreme caution as this has a specific section of the <a rel="noreferrer noopener" href="https://www.vmware.com/resources/compatibility/search.php?deviceCategory=vsan" target="_blank">VMware HCL for vSAN Ready Nodes and for Build Your Own</a>

### VMware Product Interoperability Matrix overview {#Interoperability-Guide}

I did a research on which versions would be compatible with vSphere 6.7 and vSphere 7.0 as I was not yet sure if would be able to upgrade to vSphere 7.0

  * <a rel="noreferrer noopener" href="https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&88=&2=3496,3457" target="_blank">vRealize Log Insight vs vCenter Server</a>
  * <a rel="noreferrer noopener" href="https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&88=&116=" target="_blank">vRealize Log Insight vs vRealize Operations Manager</a>
  * <a href="https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&2=3496,3457&35=" target="_blank" rel="noreferrer noopener">vRealize Operation Manager vs vCenter Server</a>

One product I forgot about was VMware Horizon. I&#8217;m currently on 7.10 and the VMware Product Interoperability Matrix show that at least 7.12 is needed to be supported. As I currently use Horizon with a full clone this should not pose much problems and I am planning to upgrade this as well. If I would be using Linked Clones or Instant Clones this could have been worse.

### Update sequence for vSphere 7.0 and its compatible VMware products {#update-sequence}

Now that we checked all the info on whether everything would be compatible and supported after the upgrade, it is time to check the knowledge base article on the update sequence. This article shows what must be upgraded before another component can be upgraded to keep a supported environment.

## Phase 2: The upgrade

### vRealize Log Insight upgrade

vRealize Log Insight was running version 4.6. Digging back through the release notes showed me that I had to upgrade from 4.6 > 4.7(.1) > 4.8 > 8.1. The VMware Product Interoperability Matrix showed that vRealize Log Insight 8.1 was compatible with either vSphere 6.7U3 and vSphere 7.0

The upgrade process was painless. It just took a lot of time. The process itself is straight forward. Go to Administration > Management > Cluster, upload the pak file and follow the screens. In my case again and again because I did not upgrade Log Insight a long time.

### vRealize Operations Manager upgrade

Upgrading the vRealize Operations Manager node is a breeze too, mainly because it is a simple setup with only one master node. vRealize Operations Manager was running version 7.5. I missed the 8.1 release so I upgraded to 8.0 first.

There are a couple of things that need some attention.

  1. Always run the vRealize Operations Manager Pre-Upgrade Readiness Assessment Tool (APUAT pak)
  2. Make sure to upgrade the OS through the OS pak files first, then the vROps pak file
  3. As I upgraded to 8.0 I had to switch files to execute the 8.1 upgrade

The Pre-Upgrade Readiness Assessment Tool showed me warnings for two items:

  1. Validating product version Make sure to run vRealize Operations Manager &#8211; 6.6.1, 6.7, 7.0 and 7.5 Virtual Appliance upgrade, as product version is 7.5.0 Ensure product and upgrade versions meet the requirements.<figure class="wp-block-image size-large is-style-rounded">

<img decoding="async" loading="lazy" width="1024" height="51" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/04/Screenshot-from-2020-04-22-15-05-54.png?resize=1024%2C51&#038;ssl=1" alt="" class="wp-image-1105" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/04/Screenshot-from-2020-04-22-15-05-54.png?resize=1024%2C51&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/04/Screenshot-from-2020-04-22-15-05-54-980x49.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/04/Screenshot-from-2020-04-22-15-05-54-480x24.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /> </figure> 

<ol start="2">
  <li>
    Checking /dev/sda partition size. The size of the partition is less than 20GB. Increase the size of the partition to be greater or equal to 20 GB (<a rel="noreferrer noopener" href="https://kb.vmware/com/s/article/75298" target="_blank">https://kb.vmware</a><a href="https://kb.vmware/com/s/article/75298" target="_blank" rel="noreferrer noopener">.</a><a href="https://kb.vmware.com/s/article/75298" target="_blank" rel="noreferrer noopener">com/s/article/75298</a>).
  </li>
</ol><figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="1024" height="51" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/04/Screenshot-from-2020-04-22-15-17-26.png?resize=1024%2C51&#038;ssl=1" alt="" class="wp-image-1109" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/04/Screenshot-from-2020-04-22-15-17-26.png?resize=1024%2C51&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/04/Screenshot-from-2020-04-22-15-17-26-980x49.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/04/Screenshot-from-2020-04-22-15-17-26-480x24.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /> </figure> 

Both are easily addressed. The first one gives a warning to use the correct pak file when upgrading. The second one refers to a KB article that has only a couple of steps:

  * take vRealize Operations offline
  * shut down guest OS
  * increase hard disk in vCenter
  * boot Virtual Machine
  * take vRealize Operations online

After addressing both warnings I was able to upgrade.

### vCenter upgrade

Simple and easy when you are already on the VCSA with an embedded Platform Services Controller (PSC).

Run the installer and choose upgrade. Supply the source vCenter information and destination vCenter information and click Next &#8211; Next &#8211; Finish. Grab a drink and wait. It is a two part process. The first part will deploy the new machine with the chosen information and the second part will migrate the data from the old vCenter to the new vCenter.

### ESXi upgrade

Before the actual upgrade could take place I needed to be sure that everything would work after the upgrade. Within the vExpert vCommunity I had seen a nice and easy way to do this. I am sorry that I can&#8217;t give credits to the person that I got the idea from.

  * Create a bootable USB ESXi installer or use the iDRAC or equivalent technology to boot your server from the ESXi installer
  * Find an empty USB flash drive
  * Put your server in Maintenance Mode
  * Shutdown and boot your server from the USB installer or the iDRAC or equivalent technology
  * Install to the empty USB drive &#8211; BE CAREFUL not to install to the wrong location

#### Upgrade check workaround

ESXi will create a VMFS volume from the remaining local space where ESXi is installed by default. After installing I tried to add the ESXi host to vCenter but failed because it had detected the local VMFS volume from the original install and that was conflicting with the one that was still present in vCenter but disconnected. I rebooted the ESXi host, booted into the original drive, verified nothing was on the local drive in the original install and deleted the datastore. Rebooted again into the USB drive and now could add the USB installed ESXi 7.0 to vCenter. Now I was able to get a glimpse of how everything was seen from an ESXi 7.0 install. The NVMe drives I was worried about were showing all fine.

Again this is a home lab and not all components are on the VMware HCL so this adds some extra steps like checking from an actual install. This would not be necessary in a production environment where everything has green checks on the VMware HCL.

#### The actual upgrade

Upgrading ESXi hosts is done easiest through VMware vSphere &#8211; Lifecycle Manager (VMware Update Manager has been rebranded)

I imported the Dell customized ISO, created a baseline and did a Host Compliance Check. The Host Compliance Check was Incompatible and led me to the following two knowledge base articles:

  * <a rel="noreferrer noopener" href="https://kb.vmware.com/s/article/78632?lang=en_US" target="_blank">QLogic qedi (HW iSCSI) and qedf (HW FCoE) driver not present in ESXi 7.0 GA OEM Custom Images released on 2020-04-02</a>
  * <a href="https://kb.vmware.com/s/article/78487?lang=en_US" target="_blank" rel="noreferrer noopener">Important information before upgrading to vSphere 7.0 (78487)</a>

I had to remove everything based on the qedi and qedf drivers

### VMware Tools / VM Hardware Compatibility

Upgrading the VMware Tools and the VM Hardware Compatibility is the last part in the process. Determine the viability of each VM to upgrade the VMware Tools and the VM Hardware Compatibility. For most VMs this won&#8217;t pose a problem. Nevertheless there are some vendor appliances that will need to run a specific version.

### vSAN upgrade

Although vSAN is not really a separate component to upgrade, you will need to upgrade the on-disk format. This is an online upgrade that will not impact the running VMs.

## Conclusion

Good preparation of an upgrade is key !!

Upgrade Methodology checks:

  1. [Determine your BOM (Bill Of Materials)][2]
  2. [Check the documentation first][3]
      1. Read the Release Notes
      2. Read the Upgrade guides for each component
  3. [Check the HCL][4]
  4. [Check the Interoperability Guide][5]
  5. [Determine the update sequence][6]
  6. Upgrade according your plan

 [1]: #VMware Product Interoperability Matrix
 [2]: /#BOM
 [3]: /#Documentation
 [4]: /#HCL
 [5]: /#Interoperability-Guide
 [6]: /#update-sequence