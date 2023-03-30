---
title: Usefull vi commands
author: Harold Preyers
type: post
date: 2016-07-05T13:58:13+00:00
url: /usefull-vi-commands/
vantage_panels_no_legacy:
  - 'true'
siteorigin_page_settings:
  - 'a:6:{s:6:"layout";s:7:"default";s:10:"page_title";b:1;s:15:"masthead_margin";b:1;s:13:"footer_margin";b:1;s:13:"hide_masthead";b:0;s:19:"hide_footer_widgets";b:0;}'
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Linux
tags:
  - editing
  - linux
  - vi

---
Some useful commands to work with vi

<table class="wp-block-advgb-table advgb-table-frontend is-style-stripes">
  <tr>
    <td>
      i
    </td>
    
    <td>
      insert
    </td>
  </tr>
  
  <tr>
    <td>
      r
    </td>
    
    <td>
      replace
    </td>
  </tr>
  
  <tr>
    <td>
      9yl
    </td>
    
    <td>
      copy nine characters from cursor position, e.g 192.168.1.0 192.168.1 will be copied
    </td>
  </tr>
  
  <tr>
    <td>
      p
    </td>
    
    <td>
      paste what you have copied
    </td>
  </tr>
  
  <tr>
    <td>
      x
    </td>
    
    <td>
      delete character
    </td>
  </tr>
  
  <tr>
    <td>
      :wq
    </td>
    
    <td>
      write and quit (save and close)
    </td>
  </tr>
  
  <tr>
    <td>
      /search_string
    </td>
    
    <td>
      find search_string
    </td>
  </tr>
  
  <tr>
    <td>
      n
    </td>
    
    <td>
      go to next occurrence of search_string
    </td>
  </tr>
  
  <tr>
    <td>
      N
    </td>
    
    <td>
      go to previous occurrence of search_string
    </td>
  </tr>
  
  <tr>
    <td>
      dd
    </td>
    
    <td>
      delete line
    </td>
  </tr>
  
  <tr>
    <td>
      10dd
    </td>
    
    <td>
      delete next 10 lines
    </td>
  </tr>
  
  <tr>
    <td>
      yy
    </td>
    
    <td>
      copy line
    </td>
  </tr>
  
  <tr>
    <td>
      10yy
    </td>
    
    <td>
      copy next 10 lines
    </td>
  </tr>
  
  <tr>
    <td>
      r
    </td>
    
    <td>
      replace current character at cursor
    </td>
  </tr>
  
  <tr>
    <td>
      R
    </td>
    
    <td>
      replace current word at cursor
    </td>
  </tr>
</table>