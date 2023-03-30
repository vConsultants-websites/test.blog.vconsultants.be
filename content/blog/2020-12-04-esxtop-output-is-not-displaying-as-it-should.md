---
title: esxtop output is not displaying as it should
author: Harold Preyers
type: post
date: 2020-12-04T16:49:30+00:00
excerpt: esxtop output launched from a terminal emulator displays incorrectly. We will update the environment variable value so esxtop information displays correctly
url: /esxtop-output-is-not-displaying-as-it-should/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/esxtop_displaying_incorrect.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - ESXi
  - Linux
  - VMware
  - vSphere
tags:
  - esxi
  - esxtop
  - linux
  - vmware

---
When you connect to your ESXi host and you launch esxtop. You look at the esxtop output and it is not displaying as it should. Instead, it is displaying like in the below screenshot:<figure class="wp-block-image size-large is-style-default has-lightbox">

<img decoding="async" loading="lazy" width="1024" height="298" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/esxtop_displaying_incorrect.png?resize=1024%2C298&#038;ssl=1" alt="esxtop displaying incorrect" class="wp-image-1721" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/esxtop_displaying_incorrect-980x285.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/esxtop_displaying_incorrect-480x140.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /> </figure> 

Your esxtop output will be displayed correctly if you are using a terminal emulator that defaults to xterm as the TERM environment variable. Some terminal emulators will use another terminal emulator value by default, eg. xterm-256color. ESXi does not map xterm-256color to one of the values it knows, so it doesn&#8217;t know how to display the output.

There is a KB article that explains how to resolve:

<a href="https://kb.vmware.com/s/article/2001448" target="_blank" rel="noreferrer noopener">Output of esxtop defaults to non-interactive CSV with unknown TermInfo (2001448)</a>

The value of the environment variable TERM is used by the server to control how input is recognized by the system, and what capabilities exist for output.

Let us have a look first what the TERM variable is in my case:

<pre id="block-062b6e7c-44d5-4bd8-89db-a8dad2f21481" class="wp-block-code lang:shell"><code>echo $TERM</code></pre>

I am receiving the following output:<figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="998" height="97" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/echo_TERM_output.png?resize=998%2C97&#038;ssl=1" alt="echo TERM output" class="wp-image-1730" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/echo_TERM_output.png?resize=998%2C97&#038;ssl=1 998w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/echo_TERM_output-980x95.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/echo_TERM_output-480x47.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 998px, 100vw" data-recalc-dims="1" /> </figure> 

My terminal emulator tries to connect to the endpoint (ESXi) with xterm-256color. Now let&#8217;s take a look at what values this endpoint does support:<figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="1001" height="150" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/terminfo_values.png?resize=1001%2C150&#038;ssl=1" alt="terminfo_values" class="wp-image-1725" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/terminfo_values.png?resize=1001%2C150&#038;ssl=1 1001w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/terminfo_values-980x147.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/terminfo_values-480x72.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1001px, 100vw" data-recalc-dims="1" /> </figure> 

So all of the above is possible to assign to TERM. The value my terminal emulator uses is not among the supported terminfo types. So the ESXi host cannot map to any of the known and thus does not know how to display the esxtop info correctly.

When we update the TERM environment variable to xterm and try to run esxtop again, the output will show nicely formatted.

<pre class="wp-block-code"><code>TERM=xterm
echo $TERM</code></pre>

Let&#8217;s check esxtop again to make sure the outcome is as expected:<figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="1024" height="260" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/esxtop_displaying_correct.png?resize=1024%2C260&#038;ssl=1" alt="esxtop displaying correct" class="wp-image-1726" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/esxtop_displaying_correct.png?resize=1024%2C260&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/esxtop_displaying_correct-980x249.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2020/12/esxtop_displaying_correct-480x122.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" /> </figure>