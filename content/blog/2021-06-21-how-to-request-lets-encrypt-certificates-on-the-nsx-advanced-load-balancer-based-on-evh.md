---
title: How to request Let’s Encrypt certificates on the NSX Advanced Load Balancer based on EVH
author: Harold Preyers
type: post
date: 2021-06-21T15:50:38+00:00
draft: true
url: /?p=1784
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
  <label
		class="wp-post-series-box__label"
				> 
  
  <p class="wp-post-series-box__name wp-post-series-name">
    Series: <em>NSX ALB from Zero to Hero</em>
  </p></label>
</div><div class="wp-block-uagb-table-of-contents uagb-toc\_\_align-left uagb-toc\_\_columns-1 uagb-block-c3197b60 " data-scroll= "1" data-offset= "30" style="" > 

<div class="uagb-toc__wrap">
  <div class="uagb-toc__title">
    Table Of Contents
  </div>
  
  <div class="uagb-toc__list-wrap">
    <ol class="uagb-toc__list">
      <li class="uagb-toc__list">
        <a href="#previous-post">Previous Post</a><li class="uagb-toc__list">
          <a href="#prequisites">Prequisites</a><li class="uagb-toc__list">
            <a href="#the-certificate-management-profile">The Certificate Management profile</a><ul class="uagb-toc__list">
              <li class="uagb-toc__list">
                <a href="#download-the-controlscript">Download the ControlSCript</a><li class="uagb-toc__list">
                  <a href="#add-the-customized-lets-encrypt-controlscript-to-the-nsx-advanced-load-balancer">Add the customized Let&#8217;s Encrypt ControlScript to the NSX Advanced Load Balancer</a><li class="uagb-toc__list">
                    <a href="#create-a-certificate-management-profile">Create a Certificate Management Profile</a>
                  </li></ul>
                </li>
                <li class="uagb-toc__list">
                  <a href="#the-virtual-services">The Virtual Services</a><ul class="uagb-toc__list">
                    <li class="uagb-toc__list">
                      <a href="#create-the-evh-parent-vs">Create the EVH PARENT VS</a><li class="uagb-toc__list">
                        <a href="#create-the-evh-child-vs">Create the EVH Child VS</a>
                      </li></ul>
                    </li>
                  </ul>
                </li>
                
                <li class="uagb-toc__list">
                  <a href="#the-certificate">The certificate</a><ul class="uagb-toc__list">
                    <li class="uagb-toc__list">
                      <a href="#request-the-certificate">Request the certificate</a><li class="uagb-toc__list">
                        <a href="#add-the-certifcate-to-the-virtual-service">Add the certifcate to the virtual service</a>
                      </li></ul>
                    </li>
                  </ul>
                </li></ul>
              </li>
              
              <li class="uagb-toc__list">
                <a href="#re-request-certificate">Re-request certificate</a><li class="uagb-toc__list">
                  <a href="#next-post">Next POST</a>
                </li></ul>
              </li>
            </ul>
          </li></ul></ol> </div> </div> </div> 
          
          <p>
          </p>
          
          <h2>
            Previous Post
          </h2>
          
          <p class="has-text-align-justify">
            In the previous post I described a setup where a standard Virtual Service was created and a Let&#8217;s Encrypt certificate was requested to be applied on the Virtual Service. In this post, the setup is comparable but it is based on Enhanced Virtual Hosting.
          </p>
          
          <p>
            The challenge: I have only one available fixed public IP, therefore all services have to terminate on this IP. Alternatively I could use custom ports to host the separate services on but I do not want to do this that way.g
          </p>
          
          <p class="has-text-align-justify">
            In the past, I had a lot of manual work to create signed certificates signed by a public certificate provider and distribute them on the relevant systems. Now, as everything is terminated on the NSX ALB and the NSX ALB handles the certificate requests, it is an automated task.
          </p>
          
          <p>
            In the process of looking for a NSX ALB setup that fits my needs, I came to the conclusion that the Virtual Hosting type SNI doesn&#8217;t fit my needs. The only way that I could share an Virtual Service Virtual IP (VSVIP) with an L4
          </p>
          
          <p>
            I found that the Virtual Hosting type SNI doesn&#8217;t fit me needs as it is only capable of doing HTTP or HTTPS. On the other hand the Virtual Hosting type Enhanced is able to do more. It can also accept requests for ports other than 80 or 443. As I am also hosting a Horizon environment I need those other ports to be mapped.
          </p>
          
          <h2>
            Prequisites
          </h2>
          
          <p>
            In the previous post, I have noted some prerequisites to be able to successfully execute the steps outlined. The same prerequisites apply here. You can read them here: <a href="https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-alb#prerequisites" target="_blank" rel="noreferrer noopener">https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-alb#prerequisites</a>
          </p>
          
          <h2>
            The Certificate Management profile
          </h2>
          
          <h3 id="download-the-controlscript">
            Download the ControlSCript
          </h3>
          
          <p>
            The standard ControlScript is not able to work with an Enhanced Virtual Hosting setup. So modified the original Let&#8217;s Encrypt ControlScript, which you can find here: <a href="https://github.com/hpreyers/AVI/blob/main/ControlScript_LetsEncrypt_EVH.py" target="_blank" rel="noreferrer noopener">https://github.com/hpreyers/AVI/blob/main/ControlScript_LetsEncrypt_EVH.py</a>
          </p>
          
          <p>
            Save the ControlScript to your local drive.
          </p>
          
          <h3 id="add-the-customized-LE-controlscript-to-the-nsx-advanced-load-balancer">
            Add the customized Let&#8217;s Encrypt ControlScript to the NSX Advanced Load Balancer
          </h3>
          
          <p>
            In the previous post we have added the ControlScript with the &#8216;Enter Text&#8217; option, which you can find <a href="https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-alb/#add-the-LE-controlscript-to-the-nsx-advanced-load-balancer">here</a>. This time I will add the ControlScript through the &#8216;Upload File&#8217; option.
          </p>
          
          <p>
            Navigate to Templates > Scripts > ControlScripts and click CREATE
          </p><figure class="wp-block-image size-large has-lightbox">
          
          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_ControlScript.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="235" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_ControlScript.png?resize=1024%2C235&#038;ssl=1" alt="Create a ControlScript on NSX ALB" class="wp-image-1814" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_ControlScript-980x224.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_ControlScript-480x110.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p>
            Supply the script name, eg ControlScript_LetsEncrypt_EVH, and choose &#8216;Upload File&#8217;. Click &#8216;Upload File and select the &#8216;ControlScript_LetsEncrypt_EVH.py&#8217; downloaded earlier.
          </p><figure class="wp-block-image size-large has-lightbox">
          
          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="256" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript.png?resize=1024%2C256&#038;ssl=1" alt="Upload a file based ControlScript to NSX ALB" class="wp-image-1805" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript.png?resize=1024%2C256&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript-980x245.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript-480x120.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <h3>
            Create a Certificate Management Profile
          </h3>
          
          <p>
            When creating the Certificate Management profile, name the Certificate Management profile &#8216;CertMgmt_LetsEncrypt_EVH&#8217;.
          </p>
          
          <p>
            You can find how to do this in this previous post: <a href="https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-alb#create-a-certificate-management-profile" target="_blank" rel="noreferrer noopener">https://blog.vconsultants.be/how-to-request-lets-encrypt-certificates-on-the-nsx-alb#create-a-certificate-management-profile</a>
          </p>
          
          <p>
            There is one extra customization in this case. You need to add the extra Custom Parameter &#8216;parentVS&#8217; with the value &#8216;VS-PARENT&#8217; as this is the Parent Virtual Service in my case. If your parent Virtual Service has a different name, you will need to supply the correct name here.
          </p><figure class="wp-block-image size-large has-lightbox">
          
          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_add_custom_parameters.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="303" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_add_custom_parameters.png?resize=1024%2C303&#038;ssl=1" alt="Add an additional Custom Parameter to a Certificate Management profile" class="wp-image-2002" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_add_custom_parameters.png?resize=1024%2C303&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_add_custom_parameters-980x290.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_add_custom_parameters-480x142.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <h2>
            The Virtual Services
          </h2>
          
          <p>
            This is the part that is somewhat different from the previous post. We&#8217;ll create a Enhanced Virtual Hosting parent Virtual Service and then we will create a Enhanced Virtual Hosting child Virtual Service that shares an Virtual Service Virtual IP (VSVIP) with the parent. As a result, it is possible to forward all traffic to the same VSVIP and the NSX Advanced Load Balancer will distribute to the necessary endpoints.
          </p>
          
          <h3>
            Create the EVH PARENT VS
          </h3>
          
          <p>
            Now in this case we are going to create a EVH Parent
          </p>
          
          <p>
            Navigate to Applications > Virtual Services > CREATE VIRTUAL SERVICE and click &#8216;Advanced Setup&#8217;
          </p><figure class="wp-block-image size-large">
          
          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="148" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup.png?resize=1024%2C148&#038;ssl=1" alt="Create a Virtual Service through advanced setup on NSX ALB" class="wp-image-1820" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup-980x142.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup-480x69.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p>
            Create the VS with the SNI, in this example I will create &#8216;vpn.vconsultants.be&#8217;. Configure the settings page and leave the other tabs with the default settings.
          </p>
          
          <ol>
            <li>
              Supply the VS name (I use the fqdn/SNI just for manageability)
            </li>
            <li>
              Leave the checkbox &#8216;Virtual Hosting VS&#8217; unchecked (default). (We will setup a standard VS.)
            </li>
            <li>
              Leave the checkbox &#8216;Auto Allocate&#8217; checked (default). (It takes an IP from the Network pool.)
            </li>
            <li>
              Change the &#8216;Application Profile&#8217; to &#8216;System-Secure-HTTP&#8217;.
            </li>
            <li>
              Supply a &#8216;Floating IPv4&#8217;. (I use a static one so that I&#8217;m able to setup NAT to this IP on my firewall.)
            </li>
            <li>
              Select a &#8216;Network for VIP Address Allocation&#8217;. (The SE will create the VIP in this network.)
            </li>
            <li>
              Select a &#8216;IPv4 Subnet&#8217;. (Only the ones created in the Network config for VIP and SE will be available.)
            </li>
            <li>
              Change the &#8216;Application Domain Name&#8217; so that it matches the fqdn of the SNI. (This will fill automatically based on the VS Name.)
            </li>
            <li>
              Check SSL and verify that the port changes to 443
            </li>
            <li>
              Select the correct Pool
            </li>
            <li>
              Change the &#8216;SSL Profile&#8217; to &#8216;System-Standard&#8217;
            </li>
          </ol>
          
          <h3>
            Create the EVH Child VS
          </h3>
          
          <p>
            Now create the EVH child VS
          </p>
          
          <p>
            From here on
          </p>
          
          <h2>
            The certificate
          </h2>
          
          <h3>
            Request the certificate
          </h3>
          
          <p>
            Request the certificate
          </p>
          
          <h3>
            Add the certifcate to the virtual service
          </h3>
          
          <p>
            Add the certificate to the Virtual Service
          </p>
          
          <p>
            The certificate has to be added to the parent virtual service
          </p>
          
          <h2>
            Re-request certificate
          </h2>
          
          <p>
            Check it the setup is working
          </p>
          
          <p>
            Patrick Kernstock
          </p>
          
          <p>
            <a href="https://patrik.kernstock.net/2021/08/quicktip-manually-trigger-certificate-renewal-on-avi-controller-nsx-alb">QuickTip: Manually trigger certificate renewal on Avi Controller/NSX ALB</a>
          </p>
          
          <h2>
            Next POST
          </h2>
          
          <p>
            In the next post I will combine this Parent Virtual Service with Horizon and Velocloud Orchestrator
          </p>