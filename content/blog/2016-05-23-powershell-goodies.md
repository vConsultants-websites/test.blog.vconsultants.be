---
title: PowerShell goodies
author: Harold Preyers
type: post
date: 2016-05-23T10:45:41+00:00
url: /powershell-goodies/
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Microsoft
  - PowerShell
tags:
  - powershell

---
<!--more-->

Anyone knowing a bit of Linux has come across tail. For those who don&#8217;t know tail, it is a tool that will monitor text files, eg log files, for changes and display the newly added content in the terminal window. This comes in handy when troubleshooting actions and looking in the log file what has been logged. I was wondering&nbsp;how to do this in Windows.

Powershell has a similar function:

<pre class="wp-block-preformatted lang:ps decode:true">Get-Content -Wait file_name</pre>

Add -tail and a number, this will show you the last 100 lines and keep the file open to output the additions to the file:

<pre class="wp-block-preformatted lang:ps decode:true">Get-Content -Wait -tail 100 file_name</pre>