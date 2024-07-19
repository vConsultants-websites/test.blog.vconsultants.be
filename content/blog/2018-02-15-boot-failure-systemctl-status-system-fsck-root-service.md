---
title: 'boot failure: systemctl status system-fsck-root.service'
author: Harold Preyers
type: post
date: 2018-02-15
url: /370-2/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/10/fsck_error.jpg
categories:
  - home lab
  - vCenter
  - VMware
tags:
  - fsck
  - vcenter
  - vcsa

---

I had downtime in my lab due to an power failure which resulted in a boot failure of my VCSA 6.5 appliance. Looking on the console showed me a "[FAILED] Failed to start File System Check on /dev/dis...uuid/uuid. See 'systemctl status system-fsck-root.service' for details." message. Therefor it booted into 'Emergency Shell' or 'Emergency mode'.
![](../../images/fsck_error.jpg)

I ran the command 'systemctl status systemd-fsck-root' manually. This showed me that the '/dev/sda3' partition was having issues.

{{% notice info "Info" %}}
**UPDATE**: It also states "RUN fsck MANUALLY". I did not notice this the first time src="https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/02/fsck_error2-1024-449.jpg" title_text="fsck_error2"
{{% /notice %}}

[][1]I tried to run fsck with no options to see if the command was known to the CLI. I then ran the command with the partition as a parameter 'fsck /dev/sda3'. I answered 'y(es)' to all 'Fix<y>?' questions.
https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/02/fsck\_error4-1024-727.jpg" title_text="fsck_error4"

In the end I received the message 'FILE SYSTEM WAS MODIFIED' and tried to reboot. The reboot command gave me an error so I went through the ESXi to reset the virtual machine. Afterwards I was able to login again.
https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/02/fsck_error5-1024-69.jpg" title_text="fsck_error5"

 [1]: https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/02/fsck_error2.jpg