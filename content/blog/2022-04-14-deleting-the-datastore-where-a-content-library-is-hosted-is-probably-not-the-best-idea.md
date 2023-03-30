---
title: Deleting the datastore where a content library is hosted is probably not the best idea
author: Harold Preyers
type: post
date: 2022-04-14T15:14:21+00:00
excerpt: 'Deleting the datastore where a Content Library is hosted is probably not the best idea but it is resolvable. '
url: /deleting-the-datastore-where-a-content-library-is-hosted-is-probably-not-the-best-idea/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-storage-ids.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Content Library
  - home lab
  - vCenter
  - VMware
tags:
  - content library
  - homelab
  - vcenter
  - vsphere

---
Deleting the datastore where a content library is hosted is probably not the best idea but &#8230; yes stupid error and now what. If you are not faint of heart (and now how to take a snapshot), you can rectify this. You should contact GSS as there is not documented solution and this might break.

Take a snapshot and verify if the vCenter backups are in a healthy status. Yes? Ok go ahead.

Log on to the vCenter and create a new Content Library and name it &#8216;i-made-an-error&#8217;. Use the new datastore you want to use and keep the rest of the settings default as these don&#8217;t really matter.

Open an SSH session to the vCenter and connect to the Postgress DB &#8216;VCDB&#8217;

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:pgsql decode:true " title="Connect to the vCenter database">psql -d VCDB -U postgres</pre>
</div>

To show which tables are present within the database:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:pgsql decode:true " title="Show all tables in the database">\d</pre>
</div>

Show an overview of the Content Libraries added ( make sure to add the trailing **;**):

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:pgsql decode:true " title="Show all the Content Libraries">SELECT id,name FROM cl_library;</pre>
</div><figure class="wp-block-image size-large is-style-default">

[<img decoding="async" loading="lazy" width="1024" height="195" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-1.png?resize=1024%2C195&#038;ssl=1" alt="Show the Content Library entries in the vCenter database" class="wp-image-2484" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-1.png?resize=1024%2C195&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-1-980x186.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-1-480x91.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" />][1]</figure> 

Now that we have an overview of the Content Libraries, with the one that is throwing an error highlighted.

In the following overview we find the library id from the new Content Library we just added and also the corresponding storage id.

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:pgsql decode:true " title="Show all the Content Libraries and their storage" >SELECT * FROM cl_library_storage;</pre>
</div><figure class="wp-block-image size-large is-style-default">

[<img decoding="async" loading="lazy" width="1024" height="197" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-storage-ids.png?resize=1024%2C197&#038;ssl=1" alt="Database Content Library storage ids" class="wp-image-2483" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-storage-ids.png?resize=1024%2C197&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-storage-ids-980x189.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-storage-ids-480x92.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" />][2]</figure> 

I will update the storage id from the faulty one we found on the previous screenshot with the one we found for the new Content Library.

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:pgsql decode:true " title="Update the storage id from the faulty Content Library">UPDATE cl_library_storage
SET storage_id = 'ae80d942-d7e2-4ab8-b0c1-93405ff4db38'
WHERE library_id = '6c7851ed-6166-44b9-b439-5bcd2f9742eb';</pre>
</div><figure class="wp-block-image size-large">

[<img decoding="async" loading="lazy" width="1024" height="128" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/update-db-content-library-storage-id.png?resize=1024%2C128&#038;ssl=1" alt="Update the storage id for the faulty Content Library" class="wp-image-2485" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/update-db-content-library-storage-id.png?resize=1024%2C128&#038;ssl=1 1024w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/update-db-content-library-storage-id-980x123.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/update-db-content-library-storage-id-480x60.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" />][3]</figure> 

There are a couple of places that helped me in solving this:

<a href="https://communities.vmware.com/t5/VMware-vCenter-Discussions/Content-library-item-delete-issue/td-p/2266050" target="_blank" rel="noreferrer noopener">https://communities.vmware.com/t5/VMware-vCenter-Discussions/Content-library-item-delete-issue/td-p/2266050</a>

<a href="https://tinkertry.com/how-to-remove-vmware-vsphere-zombie-datastore" target="_blank" rel="noreferrer noopener">https://tinkertry.com/how-to-remove-vmware-vsphere-zombie-datastore</a>

<a href="https://vmninja.wordpress.com/2019/04/05/remove-inaccessible-datastore-from-inventory/" target="_blank" rel="noreferrer noopener">https://vmninja.wordpress.com/2019/04/05/remove-inaccessible-datastore-from-inventory/</a>

 [1]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-1.png?ssl=1
 [2]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/db_content-library-storage-ids.png?ssl=1
 [3]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2022/04/update-db-content-library-storage-id.png?ssl=1