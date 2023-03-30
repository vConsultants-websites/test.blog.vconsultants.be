---
title: How to setup NSX ALB EVH combined with a horizon environment
author: Harold Preyers
type: post
date: 2021-06-17T10:40:38+00:00
draft: true
url: /?p=1799
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/architecture_1.jpg
categories:
  - AVI
  - "Let's Encrypt"
  - NSX ALB
  - VMware
tags:
  - AVI
  - Howto
  - 'Let&#039;s Encrypt'
  - NSX ALB
post_series:
  - NSX ALB from Zero to Hero
  - 'NSX ALB Let&#039;s Encrypt certs'

---
<div class="wp-post-series-box series-nsx-alb-from-zero-to-hero">
  <input id="collapsible-series-nsx-alb-from-zero-to-hero642151e99b138" class="wp-post-series-box__toggle_checkbox" type="checkbox" /> <label
		class="wp-post-series-box__label"
					for="collapsible-series-nsx-alb-from-zero-to-hero642151e99b138"
			tabindex="0"
				> 
  
  <p class="wp-post-series-box__name wp-post-series-name">
    Series: <em>NSX ALB from Zero to Hero</em>
  </p></label>
</div>

This post builds on the first post where a standard Virtual Service was created and a certificate was requested to be applied on the VS. Goal two is a little more complicated. I have one assigned public IP and this IP needs to host all of my services. Therefor I need a solution that fits.

I need one system that splits and handles all the communication. The current setup has a lot more manual steps. So I have a CertBot based system that fetches the Let&#8217;s Encrypt certificates. When the certs are renewed I need to go to the UAGs for my Horizon environment and update the certs manually. I just haven&#8217;t had the time to automate this through an API-based setup which would need some script to be run regularly.

I found that the Virtual Hosting type SNI doesn&#8217;t fit me needs as it is only capable of doing HTTP or HTTPS. As an alternative there is the Virtual Hosting type Enhanced which is able to do more. It can accept requests for other ports than 80 or 443. As I am also hosting a Horizon environment I need those other ports to be mapped.

I modiefied the original LE ControlScript, which you can find here @ <a href="https://github.com/hpreyers/AVI/blob/main/LE_Cert_Mgmt_EVH.py" target="_blank" rel="noreferrer noopener">https://github.com/hpreyers/AVI/blob/main/LE_Cert_Mgmt_EVH.py</a>

Create and add the ControlScript

You can find how to do this [here][1].

Create a Certificate Management profile @ Templates > Security > Certificate Mangement

Name LetsEncrypt_EVH

Blog 1: Setup AVI VS and request a Let&#8217;s Encrypt certificate<br><a href=&#8221;https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-advanced-load-balancer&#8221;>https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-advanced-load-balancer</a><br>Blog 2: Setup AVI EVH and request a Let&#8217;s Encrypt certificate with a customized script (should go out in the next few days)<br>https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-advanced-load-balancer-based-on-EVH<br>Blog 3: Setup AVI EVH combined with a Horizon environment (should go out next week)<br>https://blog.vconsultants.be/how-to-setup-avi-evh-combined-with-a-horizon-environment

 [1]: https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-alb#create-and-add-the-controlscript-to-the-nsx-advanced-load-balancer