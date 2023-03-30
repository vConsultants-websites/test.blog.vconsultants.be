---
title: my helm apps won’t deploy because of pvc issues
author: Harold Preyers
type: post
date: 2021-03-22T14:16:19+00:00
excerpt: "My kubernetes app wouldn't deploy because the default storage class was not specified when I created a TKG cluster on vSphere with Tanzu."
url: /my-helm-apps-wont-deploy-because-of-pvc-issues/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-163804.png
categories:
  - VMware
  - vSphereWithTanzu
tags:
  - PerstitentVolumeClaim
  - PVC
  - TanzuKubernetesCluster
  - TanzuKubernetesGrid
  - TKG
  - vSphereWithTanzu

---
Today I was playing around with vSphere with Tanzu. I want to consume vSphere with Tanzu and therefore I try to deploy an app from the bitnami repository. This should be pretty easy to do. Well I&#8217;m still in the learning phase so bear with me if this is something obvious &#8230;

These are the steps I&#8217;m doing

  * Install helm
  * Add bitnami repo
  * Install app from the bitnami repo
  * Deploy an app from the bitnami repo on a Tanzu Kubernetes Grid (TKG) cluster (deployed on vSphere with Tanzu)

So I tried to deploy redis to the TKG cluster. It needs a Persistent Volume (PV) so at deploy time a Persistent Volume Claim (PVC) would be issued and a PV should be assigned. When I saw it took a while to get my redis app deployed I looked at the namespace &#8211; Monitor &#8211; Events &#8211; Kubernetes and saw that there was an error: &#8216;no persistent volumes available for this claim and no storage class is set&#8217;.<figure class="wp-block-image size-large">

[<img decoding="async" loading="lazy" width="1024" height="50" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-163804.png?resize=1024%2C50&#038;ssl=1" alt="Persistent Volume Claim failure" class="wp-image-1752" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-163804-980x48.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-163804-480x24.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" />][1]</figure> 

Ok that is that, but what does that mean? I had no clue, so I just googled and came to [@][2]<a href="https://twitter.com/anthonyspiteri" target="_blank" rel="noreferrer noopener">anthonyspiteri</a> his blog post <a href="https://anthonyspiteri.net/no-persistent-volumes-available-claim-storage-class/" target="_blank" rel="noreferrer noopener">https://anthonyspiteri.net/no-persistent-volumes-available-claim-storage-class/</a> which shows that you can get around this by either specifying the storage class at helm install time or patching the TKG cluster.

In my case the issue was that I did not specify the defaultClass when I created the TKG cluster. I used the following yaml file to create the TKG cluster. The highlighted lines were not in the yaml file when I created the TKG cluster and these specify what storage class should be used by default.

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="top-margin:10 striped:false lang:yaml mark:16-18 decode:true " title="TKG cluster yaml" >apiVersion: run.tanzu.vmware.com/v1alpha1
kind: TanzuKubernetesCluster
metadata:
  name: k8s-01
  namespace: demo
spec:
  topology:
    controlPlane:
      count: 1
      class: guaranteed-small
      storageClass: storage-policy-tanzu
    workers:
      count: 3
      class: guaranteed-small
      storageClass: storage-policy-tanzu
  settings:
    storage:
      defaultClass: storage-policy-tanzu
  distribution:
    version: v1.18</pre>
</div>

So I executed (the k8s-01.yaml file has the above content)

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="lang:sh decode:true ">kubectl apply -f k8s-01.yaml</pre>
</div>

and received the following error:<figure class="wp-block-image size-large">

[<img decoding="async" loading="lazy" width="1024" height="16" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-172554.png?resize=1024%2C16&#038;ssl=1" alt="error: unable to recognize &quot;k8s-01.yaml&quot;: no matches for kind &quot;TanzuKubernetesCluster&quot; in version &quot;run.tanzu.vmware.com/v1alpha1&quot;" class="wp-image-1759" srcset="https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-172554-980x15.png 980w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-172554-480x7.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) and (max-width: 980px) 980px, (min-width: 981px) 1024px, 100vw" data-recalc-dims="1" />][3]</figure> 

As I was still in the TKG cluster context I could not change the TKG cluster spec. So I need to change the context to the namespace &#8216;demo&#8217; (where I deployed my TKG cluster)

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="top-margin:10 lang:yaml decode:true " title="TKG cluster yaml">kubectl config use-context demo</pre>
</div>

I reapplied the yaml file, changed the context again to the TKG cluster and issued the command:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="top-margin:10 lang:yaml decode:true " title="TKG cluster yaml">kubectl describe storageclass</pre>
</div>

Now we see that there is a default storage class for this cluster:<figure class="wp-block-image size-large">

[<img decoding="async" loading="lazy" width="740" height="199" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-180109.png?resize=740%2C199&#038;ssl=1" alt="" class="wp-image-1760" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-180109.png?resize=740%2C199&#038;ssl=1 740w, https://blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-180109-480x129.png 480w" sizes="(min-width: 0px) and (max-width: 480px) 480px, (min-width: 481px) 740px, 100vw" data-recalc-dims="1" />][4]</figure> 

And when I launch the deploy again:

<div class="wp-block-urvanov-syntax-highlighter-code-block">
  <pre class="top-margin:10 lang:yaml decode:true " title="TKG cluster yaml">kubectl run redis bitnami/redis</pre>
</div>

I see that the deploy is succeeding. Woohoo

UPDATE: [@][2]<a href="https://twitter.com/anthonyspiteri" target="_blank" rel="noreferrer noopener">anthonyspiteri</a> has come to the same conclusion in later blog posts

 [1]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-163804.png?ssl=1
 [2]: https://twitter.com/anthonyspiteri
 [3]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-172554.png?ssl=1
 [4]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2021/02/ksnip_20210219-180109.png?ssl=1