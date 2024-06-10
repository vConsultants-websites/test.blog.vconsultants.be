---
title: PowerShell goodies
author: Harold Preyers
type: post
date: 2016-05-23T10:45:41+00:00
url: /powershell-goodies/
categories:
  - Microsoft
  - PowerShell
tags:
  - powershell
  - microsoft

---
<!--more-->

Anyone knowing a bit of Linux has come across tail. For those who don't know tail, it is a tool that will monitor text files, eg log files, for changes and display the newly added content in the terminal window. This comes in handy when troubleshooting actions and looking in the log file what has been logged. I was wondering&nbsp;how to do this in Windows.

Powershell has a similar function:

```powershell
Get-Content -Wait file_name
```

Add -tail and a number, this will show you the last 100 lines and keep the file open to output the additions to the file:

```powershell
Get-Content -Wait -tail 100 file_name
```