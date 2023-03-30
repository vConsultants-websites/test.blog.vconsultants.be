---
title: 'Quick tip: Setting up TrueSSO?'
author: Harold Preyers
type: post
date: 2023-03-02T17:40:40+00:00
excerpt: Are you setting up TrueSSO? Are you looking to use signed certificates all the way in your Horizon deployment?
url: /quick-tip-setting-up-truesso/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2023/03/TrueSSO_scalability.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Horizon
  - Microsoft
  - UAG
  - VMware
tags:
  - horizon

---
<p class="has-text-align-justify">
  Are you setting up TrueSSO? Are you looking to use signed certificates to secure the communication between the Connection Server and the Enrollment Server?
</p>

Try to find the documentation on using signed certificates to secure that communication. I challenge you, you will not find it easily.

### What and why?

<p class="has-text-align-justify">
  You are allowing access to the Unified Access Gateway from the internet. You will want those services to have signed certificates to secure the communication, which will turn that icon in the Horizon client green. To enable end-to-end signed communication, you will need to make sure that you have certs all the way. In the end you are creating tunnels to backend services.
</p>

<p class="has-text-align-justify">
  On top of that you want to add TrueSSO in the equation as you want a seamless sign-on experience. This means more certificates. You follow the guides (and all the blog posts that are built using this information), so you are almost there.
</p>

<p class="has-text-align-justify">
  However, one step is exporting the &#8216;vdm.ec&#8217; certificate from the Connection Server and import it on the Enrollment Server. That is exactly where the information is missing or at least hard to be found. None of them actually talk about CA signed certificates for this. You are doing this kind of effort to get all those components (Microsoft) CA signed. Don&#8217;t you think that you should use signed certificates here as well, if . I think so!
</p>

### Where can I find the documentation

<p class="has-text-align-justify">
  Here is the documentation on the VMware websites on setting up TrueSSO:
</p>

  * Tech Zone: <a href="https://techzone.vmware.com/resource/horizon-configuration#setting-up-true-sso" target="_blank" rel="noreferrer noopener">techzone.vmware.com</a>
  * Documentation site: <a href="https://docs.vmware.com/en/VMware-Horizon/2209/horizon-console-administration/GUID-920C70D6-90B6-44A1-95E4-6A87E791C97F.html" target="_blank" rel="noreferrer noopener">docs.</a><a href="https://docs.vmware.com/en/VMware-Horizon/2212/horizon-console-administration/GUID-920C70D6-90B6-44A1-95E4-6A87E791C97F.html" target="_blank" rel="noreferrer noopener">vmware</a><a href="https://docs.vmware.com/en/VMware-Horizon/2209/horizon-console-administration/GUID-920C70D6-90B6-44A1-95E4-6A87E791C97F.html" target="_blank" rel="noreferrer noopener">.com</a>

&#8230; and also some great blogs articles out there:

  * <a href="https://my-virt.alfadir.net/index.php/2022/02/16/adfs-with-vmware-unified-access-gateway-uag/" target="_blank" rel="noreferrer noopener">Eric Monjoin (VMware PSO consultant)</a>
  * <a href="https://www.carlstalhood.com/vmware-horizon-true-sso-uag-saml/" target="_blank" rel="noreferrer noopener">Carl Stalhood</a>

<p class="has-text-align-justify">
  Search no more, you can find it <a href="https://docs.vmware.com/en/VMware-Horizon/2212/horizon-installation/GUID-2F04FCCB-B137-4CE6-B57F-596702A0A361.html" target="_blank" rel="noreferrer noopener">here</a> on the docs.vmware.com site, it is just in another section and a bit hard to find.
</p>