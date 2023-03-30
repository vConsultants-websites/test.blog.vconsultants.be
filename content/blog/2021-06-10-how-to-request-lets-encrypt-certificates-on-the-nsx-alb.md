---
title: How to request Let’s Encrypt certificates on the NSX Advanced Load Balancer
author: Harold Preyers
type: post
date: 2021-06-10T16:49:17+00:00
excerpt: "I'll request Let's Encrypt certificates via a ControlScript on two types of Virtual Services."
url: /how-to-request-lets-encrypt-certificates-on-the-nsx-alb/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/architecture_1.jpg
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
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
    This is post 1 of 1 in the series <em>&ldquo;NSX ALB from Zero to Hero&rdquo;</em>
  </p></label>
</div><div class="wp-block-uagb-table-of-contents uagb-toc\_\_align-left uagb-toc\_\_columns-1 uagb-block-552760cc " data-scroll= "1" data-offset= "30" style="" > 

<div class="uagb-toc__wrap">
  <div class="uagb-toc__title">
    Table Of Contents
  </div>
  
  <div class="uagb-toc__list-wrap">
    <ol class="uagb-toc__list">
      <li class="uagb-toc__list">
        <a href="#intro">INTRO</a><li class="uagb-toc__list">
          <a href="#prequisites">Prequisites</a><li class="uagb-toc__list">
            <a href="#what-does-the-controlscript-do">What does the ControlScript do?</a><li class="uagb-toc__list">
              <a href="#download-the-controlscript">Download the ControlScript</a><li class="uagb-toc__list">
                <a href="#add-the-lets-encrypt-controlscript-to-the-nsx-advanced-load-balancer">Add the Let&#8217;s Encrypt ControlScript to the NSX Advanced Load Balancer</a><li class="uagb-toc__list">
                  <a href="#create-a-certificate-management-profile">Create a Certificate Management profile</a><li class="uagb-toc__list">
                    <a href="#create-the-virtual-service">Create the Virtual Service</a><li class="uagb-toc__list">
                      <a href="#request-a-lets-encrypt-certificate-for-the-nsx-alb-virtual-service">Request a Let&#8217;s Encrypt certificate for the NSX ALB Virtual Service</a><li class="uagb-toc__list">
                        <a href="#add-the-lets-encrypt-certificate-to-the-nsx-alb-virtual-service">Add the Let&#8217;s Encrypt certificate to the NSX ALB Virtual Service</a><li class="uagb-toc__list">
                          <a href="#next-post">Next POST</a></ol> </div> </div> </div> <h3>
                            INTRO
                          </h3>
                          
                          <p>
                            Lately, I have been doing quite some work on VMware vSphere with Tanzu. A prerequisite to configure vSphere with Tanzu is a load balancer of some sort. Currently the following are supported, HAProxy, the NSX-T integrated load balancer or the NSX Advanced Load Balancer (ALB). (Support for the NSX ALB was added with the release of vSphere 7 U1.)
                          </p>
                          
                          <p>
                            The endgoal of the setup is to host several websites in combination with a Horizon environment on a single IP. Because not all systems can handle Let&#8217;s Encrypt requests, eg UAG, I want one system that handles the certificate request and does the SSL offloading for the endpoints. So I was looking for a load balancer solution with Let&#8217;s Encrypt ability, The NSX Advanced Load Balancer (ALB) adds the ability to request Let&#8217;s Encrypt certificates through ControlScripts.
                          </p>
                          
                          <p>
                            I already learned a lot on the NSX ALB and having some experience with other brands of load balancers certainly helped me to get up to speed quickly.
                          </p>
                          
                          <p>
                            The goal of this post is to set up a standard Virtual Service (VS) and request a Let&#8217;s Encrypt certificate for that VS. You will see that it is quite easy.
                          </p>
                          
                          <h3 id="prerequisites">
                            Prequisites
                          </h3>
                          
                          <p>
                            I will not configure some necessary configuration settings. They are, however, required to successfully execute the steps below. I will assume the following prerequisites are in place.
                          </p>
                          
                          <p>
                            The following post shows how to deploy the NSX Advanced Load Balancer and how to configure a &#8216;VMware vCenter/vSphere ESX&#8217; cloud.
                          </p>
                          
                          <p>
                            <a href="https://www.virtualizationhowto.com/2021/06/avi-load-balancer-vmware-standalone-install/">https://www.virtualizationhowto.com/2021/06/avi-load-balancer-vmware-standalone-install/</a>
                          </p>
                          
                          <ol>
                            <li>
                              The NSX ALB registered with a cloud. I use a &#8216;VMware vCenter/vSphere ESX&#8217; cloud
                            </li>
                            <li>
                              A public DNS entry for the Virtual Service. (Let&#8217;s Encrypt needs to be able to check your Virtual Service)
                            </li>
                            <li>
                              Some way to get to the virtual service from the internet. I have setup a NAT rule on my firewall for this.
                            </li>
                            <li>
                              Server Pool. (Needed to create the Virtual Service. It is obvious that the Virtual Service needs some endpoint to send the requests to.)
                            </li>
                            <li>
                              Network config for VIP and SE. (Once you configure a &#8216;VMware vCenter/vSphere ESX&#8217; cloud, you&#8217;ll have access to the networks known to vCenter. You will need to configure &#8216;Subnets&#8217; and &#8216;IP Address Pools&#8217; for the NSX ALB to use for the VSs.)
                            </li>
                            <li>
                              IPAM/DNS Profile. (You need to add the Domain Names for the Virtual Services here.)
                            </li>
                          </ol>
                          
                          <p>
                            I will cover these in a later post but for now I added them as a prerequisite.
                          </p>
                          
                          <h3 id="what-does-the-controlscript-do">
                            What does the ControlScript do?
                          </h3>
                          
                          <p>
                            The ControlScript generates a challenge token for the Let&#8217;s Encrypt servers to check the service. Secondly, it searches for a Virtual Service with an fqdn with the Common Name supplied on the certificate request. Once it finds that Virtual Service, it checks if it is listening on port 80. If not, it configures the Virtual Service to handle the request on port 80. Then it adds the challenge token to the Virtual Service. Finally, after a succesful certificate request the changes are cleared.
                          </p>
                          
                          <h3 id="download-the-controlscript">
                            Download the ControlScript
                          </h3>
                          
                          <p>
                            Download the controlscript here (either copy the contents or download the file): <a href="https://github.com/avinetworks/devops/blob/master/cert_mgmt/letsencrypt_mgmt_profile.py" target="_blank" rel="noreferrer noopener">https://github.com/avinetworks/devops/blob/master/cert_mgmt/letsencrypt_mgmt_profile.py</a>
                          </p>
                          
                          <h3 id="add-the-LE-controlscript-to-the-nsx-advanced-load-balancer">
                            Add the Let&#8217;s Encrypt ControlScript to the NSX Advanced Load Balancer
                          </h3>
                          
                          <p>
                            Navigate to Templates > Scripts > ControlScripts and click CREATE
                          </p><figure class="wp-block-image size-large is-style-default has-lightbox">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_ControlScript.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="235" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_ControlScript.png?resize=1024%2C235&#038;ssl=1" alt="Create a ControlScript for Let's Encrypt on the NSX Advanced Load Balancer" class="wp-image-1814" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_ControlScript-980x224.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_ControlScript-480x110.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p>
                            Supply the script name, eg ControlScript_LetsEncrypt_VS, and choose either &#8216;Enter Text&#8217; or &#8216;Upload File&#8217;. Now we will choose the &#8216;Enter Text&#8217; option and paste the contents of the python script on github.
                          </p><figure class="wp-block-image size-large has-lightbox">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript_VS.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="697" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript_VS.png?resize=1024%2C697&#038;ssl=1" alt="Upload a text based ControlScript to NSX Advanced Load Balancer" class="wp-image-1807" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript_VS.png?resize=1024%2C697&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript_VS-980x667.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Upload_ControlScript_VS-480x327.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <h3 id="create-a-certificate-management-profile">
                            Create a Certificate Management profile
                          </h3>
                          
                          <p>
                            Navigate to Templates > Security > Certificate Mangement and click CREATE
                          </p><figure class="wp-block-image size-large has-lightbox">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="202" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile.png?resize=1024%2C202&#038;ssl=1" alt="Create a Certificate Management profile " class="wp-image-1816" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile-980x193.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile-480x95.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p>
                            Enter the Name &#8216;CertMgmt_LetsEncrypt_VS&#8217; and select the Control Script &#8216;ControlScript_LetsEncrypt_VS&#8217;
                          </p><figure class="wp-block-image size-large has-lightbox">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_details.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="184" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_details.png?resize=1024%2C184&#038;ssl=1" alt="Configure the Certificate Management profile to use the Let's Encrypt ControlScript" class="wp-image-1817" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_details.png?resize=1024%2C184&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_details-980x176.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_details-480x86.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p>
                            Click &#8216;Enable Custom Parameters&#8217; and add the following:
                          </p>
                          
                          <table class="wp-block-advgb-table advgb-table-frontend is-style-stripes">
                            <tr>
                              <th style="padding-top:0">
                                <strong>Name</strong>
                              </th>
                              
                              <th>
                                <strong>Value</strong>
                              </th>
                              
                              <th>
                                <strong>Comment</strong>
                              </th>
                            </tr>
                            
                            <tr>
                              <td>
                                user
                              </td>
                              
                              <td>
                                admin
                              </td>
                              
                              <td style="border-width:0">
                              </td>
                            </tr>
                            
                            <tr>
                              <td>
                                password
                              </td>
                              
                              <td>
                                <enter your NSX ALB controller password for the admin user>
                              </td>
                              
                              <td>
                                (toggle Sensitive)
                              </td>
                            </tr>
                            
                            <tr>
                              <td>
                                tenant
                              </td>
                              
                              <td>
                                admin
                              </td>
                              
                              <td style="padding-top:0">
                                this is important, otherwise the script won&#8217;t have clue on which tenant it should be applied
                              </td>
                            </tr>
                          </table>
                          
                          <p>
                            Add the Custom Parameter &#8216;tenant&#8217; even if you only have one tenant, the default tenant (admin). I have struggled a lot with the script failing without having a clue why that was. Ultimately, after a long search and monitoring the log through tail, there was something in the logs that pointed me in this direction.
                          </p><figure class="wp-block-image size-large has-lightbox">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_custom_parameters.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="329" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_custom_parameters.png?resize=1024%2C329&#038;ssl=1" alt="Create the Custom Parameters for the ControlScript on the Certificate Management profile " class="wp-image-1819" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_custom_parameters.png?resize=1024%2C329&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_custom_parameters-980x314.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Certificate_Management_profile_custom_parameters-480x154.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p>
                            There is a possibility to add a fourth parameter &#8216;dryrun&#8217;, with value true or false. This will toggle the script to use the Let&#8217;s Encrypt staging server.
                          </p>
                          
                          <h3 id="create-the-virtual-service">
                            Create the Virtual Service
                          </h3>
                          
                          <p>
                            Navigate to Applications > Virtual Services > CREATE VIRTUAL SERVICE and click &#8216;Advanced Setup&#8217;
                          </p><figure class="wp-block-image size-full has-lightbox">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup.png?ssl=1"><img decoding="async" loading="lazy" width="1080" height="156" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup.png?resize=1080%2C156&#038;ssl=1" alt="Create a Virtual Service through advanced setup on NSX Advanced Load Balancer" class="wp-image-1820" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup.png?resize=1080%2C156&#038;ssl=1 1288w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup-1280x185.png 1280w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup-980x142.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup-480x69.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) and (max-width: 1280px) 1280px, (min-width: 1281px) 1288px, 100vw" data-recalc-dims="1" /></a></figure> <p>
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
                          </ol><figure class="wp-block-image size-large has-lightbox">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup_details.png?ssl=1"><img decoding="async" loading="lazy" width="932" height="1024" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Add_Virtual_Service_Advanced_Setup_details.png?resize=932%2C1024&#038;ssl=1" alt="Configure the NSX Advanced Load Balancer Virtual Service" class="wp-image-1821" data-recalc-dims="1" /></a></figure> <p class="orange-message">
                            <span style="text-decoration: underline;"><strong>Note:</strong></span> Item 7 is a bit awkward. Hovering over the question mark for help, it states that it is only applicable if the VirtualService belongs to an OpenStack or AWS cloud. When you don&#8217;t set this option, you cannot go forward. This confuses me somewhat, as I only use a vSphere cloud.
                          </p>
                          
                          <h3 id="request-a-certificate-for-the-virtual-service">
                            Request a Let&#8217;s Encrypt certificate for the NSX ALB Virtual Service
                          </h3>
                          
                          <p>
                            Navigate to Templates > Security > SSL/TLS Certificates > CREATE and click Application Certificate
                          </p><figure class="wp-block-image size-large has-lightbox">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Initiate_a_LetsEncrypt_certificate_request.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="228" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Initiate_a_LetsEncrypt_certificate_request.png?resize=1024%2C228&#038;ssl=1" alt="Initiate a Let's Encrypt certificate request from the NSX Advanced Load Balancer." class="wp-image-1824" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Initiate_a_LetsEncrypt_certificate_request-980x218.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Initiate_a_LetsEncrypt_certificate_request-480x107.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p>
                            Fill in the details for the Certificate Request (CSR) with the SNI for the certificate you want to request. The script will run when the SAVE button is clicked.
                          </p>
                          
                          <ol>
                            <li>
                              Supply the Certificate name (I use the fqdn/SNI just for manageability)
                            </li>
                            <li>
                              Select &#8216;Type&#8217; &#8216;CSR&#8217;.
                            </li>
                            <li>
                              Supply the certificate &#8216;Common Name&#8217;. This is where you supply the actual name of the certificate you want to request, in this case vpn.vconsultants.be.
                            </li>
                            <li>
                              Supply the certificate &#8216;Common Name&#8217; as &#8216;Subject Alternative Name&#8217;.
                            </li>
                            <li>
                              I started to use &#8216;EC&#8217; as the certificate &#8216;Algorithm&#8217; over &#8216;RSA&#8217;
                            </li>
                            <li>
                              Select a &#8216;Key Size&#8217;. Be aware that when choosing &#8216;EC&#8217; as &#8216;Algorithm&#8217;, &#8216;SECP384R1&#8217; is the latest that Let&#8217;s Encrypt supports for now.
                            </li>
                            <li>
                              Select &#8216;Certificate Management Profile&#8217; &#8216;CertMgmt_LetsEncrypt_VS&#8217;.
                            </li>
                            <li>
                              Check &#8216;Enable OCSP Stapling&#8217;, this will speed up the certificate validation process.
                            </li>
                          </ol>
                          
                          <div class="wp-block-image has-lightbox">
                            <figure class="alignleft size-full is-resized"><a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Initiate_a_LetsEncrypt_certificate_request_details.png?ssl=1"><img decoding="async" loading="lazy" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Initiate_a_LetsEncrypt_certificate_request_details.png?resize=821%2C1325&#038;ssl=1" alt="Configure the Let's Encrypt certificate request." class="wp-image-1826" width="821" height="1325" data-recalc-dims="1" /></a></figure>
                          </div>
                          
                          <p>
                          </p>
                          
                          <p>
                            Now watch the magic.
                          </p><figure class="wp-block-image size-large">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Succeeded_LetsEncrypt_certificate_request.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="36" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Succeeded_LetsEncrypt_certificate_request.png?resize=1024%2C36&#038;ssl=1" alt="The Let's Encrypt certificate request from the NSX Advanced Load Balancer succeeded." class="wp-image-1831" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Succeeded_LetsEncrypt_certificate_request-980x35.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Succeeded_LetsEncrypt_certificate_request-480x17.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p class="green-message">
                            <strong><span style="text-decoration: underline;">Note:</span> </strong>I added the Root and Intermediates certificates to the NSX ALB controller to validate the certificate. That is why the color of the circle is green.
                          </p>
                          
                          <h3 id="request-a-certificate-for-the-virtual-service">
                            Add the Let&#8217;s Encrypt certificate to the NSX ALB Virtual Service
                          </h3>
                          
                          <p>
                            A final step to do in this setup is to apply the certificate on the VS.
                          </p><figure class="wp-block-image size-large">
                          
                          <a href="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Apply_the_LetsEncrypt_certificate_on_the_VS.png?ssl=1"><img decoding="async" loading="lazy" width="1024" height="909" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Apply_the_LetsEncrypt_certificate_on_the_VS.png?resize=1024%2C909&#038;ssl=1" alt="Apply the Let's Encrypt certificate on the Virtual Service" class="wp-image-1832" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Apply_the_LetsEncrypt_certificate_on_the_VS.png?resize=1024%2C909&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Apply_the_LetsEncrypt_certificate_on_the_VS-980x870.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/06/Apply_the_LetsEncrypt_certificate_on_the_VS-480x426.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /></a></figure> <p>
                            In the end, you will have an NSX Advanced Load Balancer (ALB) Virtual Service configured with a Let&#8217;s Encrypt certificate.
                          </p>
                          
                          <h3>
                            Next POST
                          </h3>
                          
                          <p>
                            In the next post I&#8217;ll show the customized script that enables Let&#8217;s Encrypt Certificate Management for Enhanced Virtual Hosting (EVH) where the certificate will be requested for a EVH child Virtual Service.
                          </p>