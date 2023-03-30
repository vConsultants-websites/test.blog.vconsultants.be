---
title: VMware SRM 5.8.1 Embedded Database refuses to uninstall
author: Harold Preyers
type: post
date: 2016-11-08T21:24:55+00:00
url: /vmware-srm-5-8-1-embedded-database-refuses-to-uninstall/
vantage_panels_no_legacy:
  - 'true'
siteorigin_page_settings:
  - 'a:6:{s:6:"layout";s:7:"default";s:10:"page_title";b:1;s:15:"masthead_margin";b:1;s:13:"footer_margin";b:1;s:13:"hide_masthead";b:0;s:19:"hide_footer_widgets";b:0;}'
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - SRM
  - VMware
tags:
  - srm

---
VMware SRM 5.8.1 Embedded Database refuses to uninstall. Clicking uninstall in &#8216;Control Panel &#8211; Programs and Features&#8217; showed a progress bar going forward and then rolling back. Afterwards &#8216;Control Panel &#8211; Programs and Features&#8217; showed that the embedded PostgreSQL was still installed.

So I tried it through the command line with the purpose of generating a log file:

<pre class="lang:batch decode:true ">msiexec /l*a c:\temp\uninst.log /x VMware-SRM-Postgres.msi</pre>

The log file shows a 1603 error code in the end but msiexec error 1603 is a very generic failure error which does not give a direction to search for.

Microsoft msiexec error codes:

<table summary="table">
  <tr>
    <td data-th="Error code">
      ERROR_INSTALL_FAILURE
    </td>
    
    <td data-th="Value">
      1603
    </td>
    
    <td data-th="Description">
      A fatal error occurred during installation.
    </td>
  </tr>
</table>

<https://msdn.microsoft.com/en-us/library/windows/desktop/aa376931(v=vs.85).aspx>

Going up in the log file, somewhere halfway there is a remark that the &#8220;C:\ProgramData\VMware\VMware vCenter Site Recovery Manager Embedded Database\data\postgresql.conf&#8221; file cannot be found.

I created the postgresql.conf file in the &#8220;C:\ProgramData\VMware\VMware vCenter Site Recovery Manager Embedded Database\data\&#8221; and tried the msiexec uninstall again. Now the uninstall succeeded.