---
title: ' Setting up the lab in Ravello – Part 1 : the jumphost'
author: Harold Preyers
type: post
date: 2018-04-13T13:59:43+00:00
url: /setting-up-the-lab-in-ravello-part-1/
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:5:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";s:18:"et-gf-abhaya-libre";s:36:"Abhaya+Libre:regular,500,600,700,800";}s:6:"subset";a:8:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";i:7;s:7:"sinhala";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Linux
  - PowerShell
  - Ravello
  - VMware
tags:
  - lab
  - linux
  - ravello
  - vmware

---
\[et\_pb\_section bb\_built=&#8221;1&#8243; \_builder\_version=&#8221;3.0.47&#8243; collapsed=&#8221;off&#8221; next\_background\_color=&#8221;#000000&#8243;\]\[et\_pb\_row custom\_padding=&#8221;25px|0px|27px|0px&#8221; \_builder\_version=&#8221;3.0.47&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221; collapsed=&#8221;off&#8221;\]\[et\_pb\_column type=&#8221;4\_4&#8243;\]\[et\_pb\_post\_title comments=&#8221;off&#8221; \_builder\_version=&#8221;3.0.106&#8243; title\_font=&#8221;||||||||&#8221; title\_font\_size=&#8221;35px&#8221; title\_font\_size\_last\_edited=&#8221;on|phone&#8221; meta\_font=&#8221;||||||||&#8221; text\_orientation=&#8221;center&#8221; text\_shadow_style=&#8221;preset3&#8243;\]

&nbsp;

\[/et\_pb\_post\_title\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  In these series we will create a lab with multiple components, a jumphost, vcsa, esxi, a vsan enabled cluster, nsx and maybe more. The aim of the series is to learn about deploying all components onto the Ravello cloud.
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<h1 style="text-align: center;">
  Part 1: Creating the Jumphost
</h1>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  Part one of the series will be about creating the jumphost. I’m looking at a linux system as we do not need any license to run it and it is already available in Ravello
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<h2 style="text-align: center;">
  Creating the Ravello Application
</h2>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  The first step is to create an application. We will create a 0.1 version of the LAB:
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Create-Application.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<h2 style="text-align: center;">
  Creating the Jumphost VM in the Application
</h2>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  Drag a ‘Xubuntu Desktop 14.04.1 with qemu-kvm pre-installed’ onto the Canvas. Once the VM has been dragged onto the Canvas, there will be an error: ‘Key pair must be supplied’
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Xubuntu-VM-error-1.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  You can see that the error has its source on the General tab. To correct this a Key Pair must be created.
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Xubuntu-VM-error-2.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  On the General tab – Cloud Init Configuration – Key Pair
</p>

<p style="text-align: justify;">
  Select the Option: Create a Key Pair
</p>

<p style="text-align: justify;">
  In the following screenshot you can see that I already created a Key Pair
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Xubuntu-Key-Pair.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  Once created the private key will be available for download. To be able to use the private key with a ssh session from putty, you will need to convert the key.pem to key.ppk. Open puttygen and load the key.pem file and save the file as key.ppk.
</p>

<p style="text-align: justify;">
  Now that we have created our key pair we can save the VM and the error should disappear.
</p>

<p style="text-align: justify;">
  On the System tab, change the # CPU to 2 and the memory to 3 GB.
</p>

<p style="text-align: justify;">
  On the Disks and NICs tab we leave everything as is.
</p>

<p style="text-align: justify;">
  On the Services tab, Add Supplied Service. We will use this Service to connect to the VM via RDP.
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Add-Supplied-Service-1.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  A second service will be added. I changed the name to RDP and chose protocol RDP which sets the Port to 3389.
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Add-Supplied-Service-2.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<p style="text-align: justify;">
  We are ready to publish the application:
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Publish-LAB-1.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Publish-LAB-2.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  Change the ‘Schedule application to stop in:’ countdown timer to ‘04:00hr’. This will give us the time to update and change the VM to our needs.
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Publish-LAB-3.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  Publish will power on the VM. When Powered on we will have access to the Console. Powering on the VM takes a couple of minutes.
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/VM-is-Started.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<h2 style="text-align: center;">
  Customizing the Jumphost VM
</h2>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; background\_size=&#8221;initial&#8221; background\_position=&#8221;top\_left&#8221; background\_repeat=&#8221;repeat&#8221;\]

<h3 style="text-align: center;">
  Upgrades
</h3>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  The Console will open in a new tab. The initial password for this VM is ‘ravelloCloud’.
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/VM-Console.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  The first thing we will do is upgrade the VM to the latest release available. Open the ‘<a href="http://byobu.co/">Byobu Terminal</a>’.
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/Byobu-Terminal.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  Run the command ‘sudo apt-get update && sudo apt-get upgrade’ and confirm you want to upgrade all proposed packages. I tried do-release-upgrade first, which failed because of an apt dependency.
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  sudo apt-get update && sudo apt-get upgrade
</p>

\[/et\_pb\_text\]\[et\_pb\_code \_builder\_version=&#8221;3.0.106&#8243; /\][et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/apt-update.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  Now we are ready to upgrade to the lastest release. Confirm to all new version configuration files from the package maintainer. In the end all obsolete packages can be removed and reboot when finished.
</p>

\[/et\_pb\_text\]\[et\_pb\_image src=&#8221;https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/04/do-release-upgrade.png&#8221; align=&#8221;center&#8221; \_builder\_version=&#8221;3.0.106&#8243; animation_style=&#8221;fade&#8221;\]

&nbsp;

\[/et\_pb\_image\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  Run the command ‘sudo apt-get dist-upgrade’ and confirm you want to upgrade all proposed packages. Now your system will be fully up-to-date.
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<h3 style="text-align: center;">
  XRDP 0.9.x
</h3>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  Install xrdp 0.9.x so that we can connect via RDP. This will be a more pleasant way of working.
</p>

<p style="text-align: justify;">
  We will add a PPA (Personal Package Archive) to add the package source location to the /etc/apt/sources.list file. This will enable updates through the apt update process. We will install the latest version of xrpd from this location. At the time of writing the version integrated is in the ubuntu sources is 0.6.x. The latest stable version has quite some enhancements like shared clipboard support.
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  sudo add-apt-repository ppa:hermlnx/xrdp<br /> sudo apt-get update<br /> sudo apt-get install<br /> xrdp xrdp -v
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  The version installed at the time of writing is 0.9.4
</p>

<p style="text-align: justify;">
  Create xsession file with contents xfce4-session. The latest xrdp version should be detecting the desktop environment by default but in my case it did&#8217;t and wouldn&#8217;t work without the following xsession file.
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  cd $HOME<br /> echo xfce4-session > ~/.xsession
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Generate new certificate and key

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  openssl req -x509 -newkey rsa:2048 -nodes -keyout key.pem -out cert.pem -days 365
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Update XRDP to use the new certificates

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  cd /etc/xrdp sudo vi xrdp.ini
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Change the following lines to use the certificate and key generated

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="text-align: justify; padding-left: 30px;">
  certificate=/home/ubuntu/cert.pem<br /> key_file=/home/ubuntu/key.pem<br /> cd /etc/X11/<br /> sudo vi wrapper.config
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Change the following line

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  allowed_users=anybody
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Reboot the VM Now you can access the VM through RDP. You will need to confirm the self-signed cert as it has not been signed by a trusted root CA.

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<h3 style="text-align: center;">
  Powershell Core
</h3>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Import the public repository GPG keys

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add &#8211;
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Register the Microsoft Ubuntu repository

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list | sudo tee /etc/apt/sources.list.d/microsoft.list
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<p style="text-align: justify;">
  Update the list of products
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  sudo apt-get update
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Install PowerShell

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  sudo apt-get install -y powershell
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Start PowerShell

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  pwsh
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<h3 style="text-align: center;">
  PowerCLI 10
</h3>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Install the PowerCLI module from the PowerShell Gallery

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  Install-Module -Name VMware.PowerCLI -scope CurrentUser
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Verify PowerCLI version

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  Get-PowerCLIVersion
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

OPTIONAL: Opt-out from the Customer Experience Improvement Program (CEIP)

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  Set-PowerCLIConfiguration -scope user -ParticipateCeip $false
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

OPTIONAL: Do not display the warning about using self-signed certificates

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

<h3 style="text-align: center;">
  OPTIONAL: Visual Studio Code
</h3>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

Installing Microsoft Visual Studio Code can be usefull for creating scripts that will/could be used within the environment.

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243; text\_font=&#8221;Abhaya Libre||||||||&#8221; text\_font\_size=&#8221;15px&#8221; background\_color=&#8221;rgba(12,113,195,0.13)&#8221; custom\_margin=&#8221;|10px||10px&#8221; custom\_padding=&#8221;10px||5px|&#8221;\]

<p style="padding-left: 30px;">
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg &#8211;dearmor > microsoft.gpg<br /> sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg sudo sh -c &#8216;echo &#8220;deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main&#8221; > /etc/apt/sources.list.d/vscode.list&#8217;<br /> sudo apt-get update<br /> sudo apt-get install code # or code-insiders
</p>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.0.106&#8243;\]

The next part will be setting up the ESXi machines and VCSA.

\[/et\_pb\_text\]\[et\_pb\_divider \_builder\_version=&#8221;3.3.1&#8243; /\][et\_pb\_text \_builder\_version=&#8221;3.3.1&#8243;]

<h3 style="text-align: center;">
  Many thanks to:
</h3>

\[/et\_pb\_text\]\[et\_pb\_text \_builder\_version=&#8221;3.3.1&#8243;\]

Dale Scriven &#8211; [http://vhorizon.co.uk/using-ravello-cloud-for-the-bandwidth-embarrassed/  
][1] Roman Dodin ([@ntdvps][2]) &#8211; [https://netdevops.me/2017/installing-xrdp-0.9.1-on-ubuntu-16.04-xenial/  
][3] Microsoft &#8211; [https://docs.microsoft.com/en-us/powershell/scripting/setup/installing-powershell-core-on-macos-and-linux?view=powershell-6#ubuntu-1604  
][4] Microsoft &#8211; [https://code.visualstudio.com/docs/setup/linux  
][5] Ravello for providing the vExpert Free Service Account – [https://cloud.ravellosystems.com][6]

\[/et\_pb\_text\]\[/et\_pb\_column\]\[/et\_pb\_row\]\[/et\_pb\_section\]\[et\_pb\_section bb\_built=&#8221;1&#8243; fullwidth=&#8221;off&#8221; specialty=&#8221;off&#8221; prev\_background\_color=&#8221;#000000&#8243;\]\[et\_pb\_row\]\[/et\_pb\_row\]\[/et\_pb_section\]

 [1]: http://vhorizon.co.uk/using-ravello-cloud-for-the-bandwidth-embarrassed/
 [2]: https://twitter.com/ntdvps
 [3]: https://netdevops.me/2017/installing-xrdp-0.9.1-on-ubuntu-16.04-xenial/
 [4]: https://docs.microsoft.com/en-us/powershell/scripting/setup/installing-powershell-core-on-macos-and-linux?view=powershell-6#ubuntu-1604
 [5]: https://code.visualstudio.com/docs/setup/linux
 [6]: https://cloud.ravellosystems.com/