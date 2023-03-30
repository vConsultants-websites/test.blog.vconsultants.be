---
title: Enable Workload Management does not finish
author: Harold Preyers
type: post
date: 2022-03-14T16:44:43+00:00
url: /enable-workload-management-does-not-finish/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/architecture_1.jpg
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - AVI
  - NSX ALB
  - VMware
  - vSphereWithTanzu
tags:
  - AVI
  - NSX ALB
  - TanzuKubernetesGrid
  - vSphereWithTanzu

---
<p class="has-text-align-justify">
  Some time ago we were having issues in the Tanzu PoC class for partners we were teaching. One of the students had an environment where the Enable Workload Management process was unable to finish the creation of the Supervisor Cluster.
</p>

<p class="has-text-align-justify">
  It was an interesting issue because when we verified all the settings we saw everything configured correctly on a UI level. Nevertheless when went to the virtualservice we saw that it was down because of the servers in the pool were not up.
</p>

When the Enable Workload Management is unable to finish, there are some usual suspects. Most of the time the details within the Enable Workload Management wizard are just not correct. Validation on the values supplied could be better I believe. You only know when it takes to long, that you need to start verifying the components. The following milestones can be checked.

  * Are the Supervisor Control Plane VMs created?
  * Do the Supervisor Control Plane VMs have the correct amount of IPs
  * Are the NSX ALB Service Engine VMs created?

<p class="has-text-align-justify">
  During the troubleshooting, we verified these usual suspects. We also verified all values supplied in the different consoles, being the Workload Management configuration page in the vSphere client but also on the NSX ALB. It seemed that this student had done everything correct. We started to exclude issues with pinging, executing curl to the relevant ip&#8217;s and checking the logs.
</p>

<p class="has-text-align-justify">
  At a moment we arrived at the Service Engines and went from there. At lunch time I stumbled onto this blog post from Nick Schmidt (a fellow vExpert), which made a jump in to the troubleshooting phase:
</p>

<a href="https://dev.to/ngschmidt/troubleshooting-with-vmware-nsx-alb-avi-vantage-23pc" target="_blank" rel="noreferrer noopener">https://dev.to/ngschmidt/troubleshooting-with-vmware-nsx-alb-avi-vantage-23pc</a>

<p class="has-text-align-justify">
  This showed how to connect to the networking namespace on the Service Engine and this helped a lot.
</p>

<p class="has-text-align-justify">
  If you do not connect to the networking namespace, you will see the configuration on an OS level. Within the networking namespace you troubleshoot within the correct context.
</p>

<p class="has-text-align-justify">
  Although the web UI shows the correct values for the configured routes, they were not applied correct on the NSX ALB SE.
</p>

Here are the steps that I executed when connected to one of the NSX ALB Service Engines:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " >ifconfig --&gt; shows the network configuration of the NSX ALB SE</pre>
</div>

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " >ip route --&gt; shows the routes, only the management route was shown</pre>
</div>

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " >ip netns show --&gt; shows the network namespaces, only one was shown in this environment, namely avi_ns1, there was also only one tenant</pre>
</div>

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " >ip netns exec avi_ns1 bash --&gt; launches a shell within the avi_ns1 namespace</pre>
</div>

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true " >ip route --&gt; shows the routes from the avi_ns1 namespace</pre>
</div>

Now we saw that there was a route missing within this namespace. We went back to the web UI deleted the route and re-created, et voila the servers in the pool came up and therefor the virtualservice was alive.