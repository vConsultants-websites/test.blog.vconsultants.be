---
title: Vsan observer generate offline bundle
author: Harold Preyers
type: post
date: 2018-10-26T06:39:12+00:00
draft: true
url: /?p=136
vantage_panels_no_legacy:
  - 'true'
categories:
  - Uncategorized

---
How to generate a VSAN Observer offline bundle when your machine doesn&#8217;t have access to the internet?

It takes some steps:

  * download the content that vsan observer goes to fetch online and put it in the correct locations
  * update&nbsp;the vsan.rb file
  * start the vsan observer offline bundle collection

VSAN observer doesn&#8217;t work when the necessary files are not there.

Vsan observer generate offline bundle

rem This script can be used to start the rvc VSAN observer from the desktop  
rem The original version was created by Duco Jaspars, see http://blog.vConsult.nl

rem vCenter Administrator account, use <username>:<password> if you don&#8217;t mind storing your passsword in a text file

SET admin=username@fqdn

rem vCenter name

SET vCenter=vcenter\_server\_fqdn

rem Datacenter object name

SET datacenter=datacenter_name

rem vCenter/vSAN Cluster name, use \ to escape spaces if you have any in your cluster name

SET cluster=cluster_name

rem path to rvc, usualy &#8220;c:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\&#8221; (with the double quotes)

SET rvcpath=&#8221;c:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\&#8221;

rem Constructed argument to pass to rvc, no need to change

SET clusterarg=%vCenter%/%datacenter%/computers/%cluster%

cd %rvcpath%

..\ruby-1.9.3-p392-i386-mingw32\bin\ruby -Ilib -Igems\backports-3.1.1\lib -Igems\builder-3.2.0\lib -Igems\highline-1.6.15\lib -Igems\nokogiri-1.5.6-x86-mingw32\lib -Igems\rbvmomi-1.7.0\lib -Igems\terminal-table-1.4.5\lib -Igems\trollop-1.16\lib -Igems\zip-2.0.2\lib bin\rvc -c &#8216;vsan.observer %clusterarg% -r -o -n -g c:\\temp\\VSAN_OBSERVER&#8217; %admin%@%vCenter%

<span style="text-decoration: underline;">&nbsp;</span>

&nbsp;

vsan observer offline mode

download files

\# Author: Harold Preyers

\# Product: VMware VSAN

\# Description: Script to download files for offline VSAN Observer mode

\# Reference:

\# I used William Lams script and converted to Powershell

\# http://www.virtuallyghetto.com/2014/10/automate-vsan-observer-offline-mode-configurations.html

&nbsp;

$EXTERNAL\_LIB\_DIR = &#8220;C:\Data\externallibs&#8221;

$jsdir = &#8220;js&#8221;

$cssdir = &#8220;css&#8221;

$fontdir = &#8220;font&#8221;

$Proxy = &#8220;http://userproxy.glb.ebc.local:8080&#8243;gen

&nbsp;

$EXTERNAL\_JS\_LIB_URLS=@(

&#8220;https://ajax.googleapis.com/ajax/libs/angularjs/1.1.5/angular.min.js&#8221;,

&#8220;https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datepicker/1.3.0/js/bootstrap-datepicker.min.js&#8221;,

&#8220;https://netdna.bootstrapcdn.com/twitter-bootstrap/2.3.1/js/bootstrap.min.js&#8221;,

&#8220;https://cdnjs.cloudflare.com/ajax/libs/d3/3.4.6/d3.min.js&#8221;,

&#8220;https://code.jquery.com/jquery-1.9.1.min.js&#8221;,

&#8220;http://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.1/jquery.min.map&#8221;,

&#8220;https://code.jquery.com/ui/1.9.1/jquery-ui.min.js&#8221;,

&#8220;https://code.jquery.com/jquery.js&#8221;

)

&nbsp;

$EXTERNAL\_CSS\_LIB_URLS=@(

&#8220;https://netdna.bootstrapcdn.com/twitter-bootstrap/2.3.1/css/bootstrap-combined.no-icons.min.css&#8221;,

&#8220;https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datepicker/1.3.0/css/datepicker.min.css&#8221;,

&#8220;https://netdna.bootstrapcdn.com/font-awesome/3.1.1/css/font-awesome.css&#8221;

)

&nbsp;

$EXTERNAL\_FONT\_LIB_URLS=@(

&#8220;http://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/fonts/fontawesome-webfont.eot&#8221;,

&#8220;http://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/fonts/fontawesome-webfont.svg&#8221;,

&#8220;http://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/fonts/fontawesome-webfont.ttf&#8221;,

&#8220;http://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/fonts/fontawesome-webfont.woff&#8221;,

&#8220;http://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/fonts/FontAwesome.otf&#8221;

)

&nbsp;

If (Test-Path $EXTERNAL\_LIB\_DIR){

Write-Host &#8220;It looks like ${EXTERNAL\_LIB\_DIR} exists already, please delete and re-run script&#8221;

$Answer = Write-Host &#8220;Delete Now (Y/N)&#8221;

If ($Answer = &#8220;Y&#8221;) {

}

Else {

If ($Answer = &#8220;N&#8221;) {

Exit

}

}

}

&nbsp;

Write-Host &#8220;\nCreating ${EXTERNAL\_LIB\_DIR}/{js,css,font} directories &#8230;&#8221;

New-Item -Path $EXTERNAL\_LIB\_DIR\$jsdir -ItemType directory

New-Item -Path $EXTERNAL\_LIB\_DIR\$cssdir -ItemType directory

New-Item -Path $EXTERNAL\_LIB\_DIR\$fontdir -ItemType directory

&nbsp;

Write-Host &#8220;\nDownloading Javascript files &#8230;&#8221;

ForEach ($entry in $EXTERNAL\_JS\_LIB_URLS){

Write-Host &#8220;\nDownloading $entry &#8230;&#8221;

$urlArray = $entry.split(&#8220;/&#8221;)

$filename = $urlArray[-1]

Invoke-WebRequest $entry -OutFile $EXTERNAL\_LIB\_DIR\$jsdir\$filename -Proxy $Proxy -ProxyUseDefaultCredentials

}

&nbsp;

Write-Host &#8220;\nDownloading CSS files &#8230;&#8221;

ForEach ($entry in $EXTERNAL\_CSS\_LIB_URLS){

Write-Host &#8220;\nDownloading $entry &#8230;&#8221;

$urlArray = $entry.split(&#8220;/&#8221;)

$filename = $urlArray[-1]

Invoke-WebRequest $entry -OutFile $EXTERNAL\_LIB\_DIR\$cssdir\$filename -Proxy $Proxy -ProxyUseDefaultCredentials

}

&nbsp;

Write-Host &#8220;\nDownloading FONT files &#8230;&#8221;

ForEach ($entry in $EXTERNAL\_FONT\_LIB_URLS){

Write-Host &#8220;\nDownloading $entry &#8230;&#8221;

$urlArray = $entry.split(&#8220;/&#8221;)

$filename = $urlArray[-1]

Invoke-WebRequest $entry -OutFile $EXTERNAL\_LIB\_DIR\$fontdir\$filename -Proxy $Proxy -ProxyUseDefaultCredentials

}

copy to vcenter server

xcopy /E /I .\externallibs &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\externallibs&#8221;

mkdir &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\OLD&#8221;

move &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\graphs.html&#8221; &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\OLD&#8221;

move &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\history.erb.html&#8221; &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\OLD&#8221;

move &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\login.erb.html&#8221; &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\OLD&#8221;

move &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\stats.erb.html&#8221; &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer\OLD&#8221;

xcopy .\graphs.html &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer&#8221;

xcopy .\history.erb.html &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer&#8221;

xcopy .\login.erb.html &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer&#8221;

xcopy .\stats.erb.html &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\observer&#8221;

xcopy .\vsan_observer.bat &#8220;C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc&#8221;

change vsan.rb file, otherwise log bundle generation will fail:

<https://blogs.vmware.com/vsphere/2014/10/virtual-san-observer-offline-monitoring-bundle-issue.html>

C:\Program Files\VMware\Infrastructure\VirtualCenter Server\support\rvc\lib\rvc\modules

Change

filenameInTar = File.join(&#8216;externallibs&#8217;, d, File.basename)

To

filenameInTar = File.join(&#8216;externallibs&#8217;, d, File.basename(f))