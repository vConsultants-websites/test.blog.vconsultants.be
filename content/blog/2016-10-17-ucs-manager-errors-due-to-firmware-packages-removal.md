---
title: UCS Manager errors due to Firmware Packages removal
author: Harold Preyers
type: post
date: 2016-10-17T13:38:25+00:00
url: /ucs-manager-errors-due-to-firmware-packages-removal/
vantage_panels_no_legacy:
  - 'true'
siteorigin_page_settings:
  - 'a:6:{s:6:"layout";s:7:"default";s:10:"page_title";b:1;s:15:"masthead_margin";b:1;s:13:"footer_margin";b:1;s:13:"hide_masthead";b:0;s:19:"hide_footer_widgets";b:0;}'
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - Cisco
  - UCS
tags:
  - UCS

---
UCS Manager is showing 309 errors because a Firmware Packages have been deleted but the references in the Host Firmware Packages (HFP) still exist.

[<img decoding="async" loading="lazy" class="alignnone wp-image-172 size-full" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_errors.png?resize=253%2C68" alt="ucs_errors" width="253" height="68" data-recalc-dims="1" />][1]

Appears that all of the errors show a cause of ‘image-deleted’. In the ‘Affected object’ the path where the error is originating is shown. In the first error it shows ‘**org-root/fw-host-pack-HFP-2.2.7/pack-image-Cisco Systems|R200-1120402W|blade-controller**’ The first portion ‘**org-root/fw-host-pack-HFP-2.2.7**’ is important because this is the path. The second part ‘pack-image-Cisco Systems|R200-1120402W|blade-controller’ is the component image which is missing.

A HFP resides in the ‘Servers’ tab. The referenced one can be found in ‘Servers – Policies – root – Host Firmware Packages – HFP-2.2.7’

[<img decoding="async" loading="lazy" class="alignnone wp-image-174 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_Faults_Summary-1024x219.png?resize=720%2C154" alt="ucs_faults_summary" width="720" height="154" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_Faults_Summary.png?resize=1024%2C219&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_Faults_Summary.png?resize=300%2C64&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_Faults_Summary.png?resize=768%2C164&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_Faults_Summary.png?w=1887&ssl=1 1887w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][2]

Going to the referenced ‘Host Firmware Packages’ some of the components have a presence status ‘Missing’

[<img decoding="async" loading="lazy" class="alignnone wp-image-153 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_detail-1024x261.png?resize=720%2C184" alt="hfp_227_detail" width="720" height="184" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_detail.png?resize=1024%2C261&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_detail.png?resize=300%2C76&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_detail.png?resize=768%2C196&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_detail.png?w=1573&ssl=1 1573w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][3]

Below is a screenshot of the existing ‘Firmware Packages’. You can see that the ‘Firmware Package’ 2.2.6f exists for the ‘B Series’ and for the ‘Infrastructure’ but not for the ‘C Series’.

Important to notice is that ‘Rack Package’ 2.2.7b’ is not present for the ‘C Series’ as you can see in the next screenshot.

[<img decoding="async" loading="lazy" class="alignnone wp-image-151 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/FP_overview-1024x297.png?resize=720%2C209" alt="fp_overview" width="720" height="209" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/FP_overview.png?resize=1024%2C297&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/FP_overview.png?resize=300%2C87&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/FP_overview.png?resize=768%2C223&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/FP_overview.png?w=1503&ssl=1 1503w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][4]

Going to the Host Firmware Package general page and looking in the assigned versions. You can see that ‘Rack Package’ 2.2(7b)C is assigned. In the above screenshot we saw that this package is not in the UCS Manager anymore.

[<img decoding="async" loading="lazy" class="alignnone wp-image-157" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_selected.png?resize=721%2C264" alt="hfp_227_selected" width="721" height="264" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_selected.png?w=970&ssl=1 970w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_selected.png?resize=300%2C110&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_selected.png?resize=768%2C281&ssl=1 768w" sizes="(max-width: 721px) 100vw, 721px" data-recalc-dims="1" />][5]

The rack package is empty. It was on ‘Rack Package’ 2.2(7b)C but because the Firmware Package was removed from UCS Manager this is showing blank.

[<img decoding="async" loading="lazy" class="alignnone wp-image-154 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_modify_package_versions-1024x624.png?resize=720%2C439" alt="hfp_227_modify_package_versions" width="720" height="439" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_modify_package_versions.png?resize=1024%2C624&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_modify_package_versions.png?resize=300%2C183&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_modify_package_versions.png?resize=768%2C468&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_modify_package_versions.png?w=1160&ssl=1 1160w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][6]

Use ‘Show Policy Usage’ to look if the Host Firmware Package is used somewhere.

[<img decoding="async" loading="lazy" class="alignnone wp-image-158" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage-300x110.png?resize=720%2C265" alt="hfp_227_show_policy_usage" width="720" height="265" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage.png?resize=300%2C110&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage.png?resize=768%2C283&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage.png?w=975&ssl=1 975w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][7]

The Host Firmware Package is used in Service Profile Template ‘HP\_FW\_TEST\_Cisco\_Support_Case’

[<img decoding="async" loading="lazy" class="alignnone wp-image-156 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_in_use-1024x573.png?resize=720%2C403" alt="hfp_227_policy_usage_detail_in_use" width="720" height="403" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_in_use.png?resize=1024%2C573&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_in_use.png?resize=300%2C168&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_in_use.png?resize=768%2C430&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_in_use.png?w=1263&ssl=1 1263w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][8]

Navigate to the Service Profile Template

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-171" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_overview.png?resize=444%2C517" alt="spt_overview" width="444" height="517" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_overview.png?w=444&ssl=1 444w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_overview.png?resize=258%2C300&ssl=1 258w" sizes="(max-width: 444px) 100vw, 444px" data-recalc-dims="1" />][9]

Verify the Policy Usage

[<img decoding="async" loading="lazy" class="alignnone wp-image-169 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_show_policy_usage-1024x508.png?resize=720%2C357" alt="spt_hp_fw_test_cisco_support_case_show_policy_usage" width="720" height="357" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_show_policy_usage.png?resize=1024%2C508&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_show_policy_usage.png?resize=300%2C149&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_show_policy_usage.png?resize=768%2C381&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_show_policy_usage.png?w=1148&ssl=1 1148w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][10]

It is not in use, so it is safe to delete the Service Profile Template

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-168" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_policy_usage_detail.png?resize=601%2C554" alt="spt_hp_fw_test_cisco_support_case_policy_usage_detail" width="601" height="554" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_policy_usage_detail.png?w=601&ssl=1 601w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_policy_usage_detail.png?resize=300%2C277&ssl=1 300w" sizes="(max-width: 601px) 100vw, 601px" data-recalc-dims="1" />][11]

Delete the Service Profile Template

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-167" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_delete.png?resize=726%2C405" alt="spt_delete" width="726" height="405" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_delete.png?w=726&ssl=1 726w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_delete.png?resize=300%2C167&ssl=1 300w" sizes="(max-width: 726px) 100vw, 726px" data-recalc-dims="1" />][12]

Going back to the Host Firmware Package and looking again at the policy usage

[<img decoding="async" loading="lazy" class="alignnone wp-image-158" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage.png?resize=720%2C265" alt="hfp_227_show_policy_usage" width="720" height="265" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage.png?w=975&ssl=1 975w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage.png?resize=300%2C110&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage.png?resize=768%2C283&ssl=1 768w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][7]

&nbsp;

It is not in use anymore

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-155" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_empty_detail.png?resize=600%2C550" alt="hfp_227_policy_usage_detail_empty_detail" width="600" height="550" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_empty_detail.png?w=600&ssl=1 600w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_empty_detail.png?resize=300%2C275&ssl=1 300w" sizes="(max-width: 600px) 100vw, 600px" data-recalc-dims="1" />][13]

Delete the Host Firmware Package

[<img decoding="async" loading="lazy" class="alignnone wp-image-152" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_delete.png?resize=720%2C556" alt="hfp_227_delete" width="720" height="556" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_delete.png?w=914&ssl=1 914w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_delete.png?resize=300%2C232&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_delete.png?resize=768%2C593&ssl=1 768w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][14]

The previous actions made UCS Manager go down to 193 errors. The next ones are about the Host Firmware Package &#8216;default&#8217;. I don’t want to delete this Host Firmware Package &#8216;default&#8217;, so I will adapt this one so it doesn’t throw any errors anymore.

The following screenshot is not entirely correct as the package was already changed to the correct one (‘2.2(5d)C’ was ‘2.2(6f)C’ as you will see in a later screenshot) but I still wanted to show that I checked the Policy Usage first:

[<img decoding="async" loading="lazy" class="alignnone wp-image-166 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_show_policy_usage-1024x302.png?resize=720%2C212" alt="hfp_default_show_policy_usage" width="720" height="212" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_show_policy_usage.png?resize=1024%2C302&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_show_policy_usage.png?resize=300%2C88&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_show_policy_usage.png?resize=768%2C226&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_show_policy_usage.png?w=1180&ssl=1 1180w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][15]

The Host Firmware Package ‘default’ is not used in a Service Profile Template, so it safe to change the assigned ‘Rack Package’.

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-165" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_policy_usage.png?resize=601%2C550" alt="hfp_default_policy_usage" width="601" height="550" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_policy_usage.png?w=601&ssl=1 601w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_policy_usage.png?resize=300%2C275&ssl=1 300w" sizes="(max-width: 601px) 100vw, 601px" data-recalc-dims="1" />][16]

Modify the Package version from the Rack package that was deleted

[<img decoding="async" loading="lazy" class="alignnone wp-image-163 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions-1024x512.png?resize=720%2C360" alt="hfp_default_modify_package_versions" width="720" height="360" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?resize=1024%2C512&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?resize=300%2C150&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?resize=768%2C384&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?resize=960%2C480&ssl=1 960w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?w=1457&ssl=1 1457w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][17]

Set it to one that still exists

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-164" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions_detail.png?resize=399%2C549" alt="hfp_default_modify_package_versions_detail" width="399" height="549" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions_detail.png?w=399&ssl=1 399w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions_detail.png?resize=218%2C300&ssl=1 218w" sizes="(max-width: 399px) 100vw, 399px" data-recalc-dims="1" />][18]

Going back to the errors lead me to the next Host Firmware Package package, which was on a sub-organisation level. Looking at the components in the Host Firmware Package I see a presence of ‘Missing’ again

[<img decoding="async" loading="lazy" class="alignnone wp-image-163 size-large" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions-1024x512.png?resize=720%2C360" alt="hfp_default_modify_package_versions" width="720" height="360" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?resize=1024%2C512&ssl=1 1024w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?resize=300%2C150&ssl=1 300w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?resize=768%2C384&ssl=1 768w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?resize=960%2C480&ssl=1 960w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png?w=1457&ssl=1 1457w" sizes="(max-width: 720px) 100vw, 720px" data-recalc-dims="1" />][17]

First I was going to modify the package version but I went to look if it was in use first.

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-162" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_DCA_HYP_227d_show_policy_usage_in_use.png?resize=602%2C549" alt="hfp_dca_hyp_227d_show_policy_usage_in_use" width="602" height="549" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_DCA_HYP_227d_show_policy_usage_in_use.png?w=602&ssl=1 602w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_DCA_HYP_227d_show_policy_usage_in_use.png?resize=300%2C274&ssl=1 300w" sizes="(max-width: 602px) 100vw, 602px" data-recalc-dims="1" />][19]

It was in use by a Service Profile Template and went to see if the Service Profile Template was in use

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-170" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_Update_SSD_to_fw_DM0T_show_policy_usage.png?resize=598%2C550" alt="spt_hp_update_ssd_to_fw_dm0t_show_policy_usage" width="598" height="550" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_Update_SSD_to_fw_DM0T_show_policy_usage.png?w=598&ssl=1 598w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_Update_SSD_to_fw_DM0T_show_policy_usage.png?resize=300%2C276&ssl=1 300w" sizes="(max-width: 598px) 100vw, 598px" data-recalc-dims="1" />][20]

It was not so I deleted the Service Profile Template and went back up the chain. The Host Firmware Package was not in use anymore. So I deleted the Host Firmware Package

[<img decoding="async" loading="lazy" class="alignnone size-full wp-image-161" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_DCA_HYP_227d_show_policy_usage_empty.png?resize=598%2C550" alt="hfp_dca_hyp_227d_show_policy_usage_empty" width="598" height="550" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_DCA_HYP_227d_show_policy_usage_empty.png?w=598&ssl=1 598w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_DCA_HYP_227d_show_policy_usage_empty.png?resize=300%2C276&ssl=1 300w" sizes="(max-width: 598px) 100vw, 598px" data-recalc-dims="1" />][21]

All references to Firmware Packages were corrected

As a result all faults are cleared:

[<img decoding="async" loading="lazy" class="alignnone wp-image-173 size-full" src="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_errors_empty.png?resize=469%2C73" alt="ucs_errors_empty" width="469" height="73" srcset="https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_errors_empty.png?w=469&ssl=1 469w, https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_errors_empty.png?resize=300%2C47&ssl=1 300w" sizes="(max-width: 469px) 100vw, 469px" data-recalc-dims="1" />][22]

 [1]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_errors.png
 [2]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_Faults_Summary.png
 [3]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_detail.png
 [4]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/FP_overview.png
 [5]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_selected.png
 [6]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_modify_package_versions.png
 [7]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_show_policy_usage.png
 [8]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_in_use.png
 [9]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_overview.png
 [10]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_show_policy_usage.png
 [11]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_FW_Test_Cisco_Support_Case_policy_usage_detail.png
 [12]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_delete.png
 [13]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_policy_usage_detail_empty_detail.png
 [14]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_227_delete.png
 [15]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_show_policy_usage.png
 [16]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_policy_usage.png
 [17]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions.png
 [18]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_default_modify_package_versions_detail.png
 [19]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_DCA_HYP_227d_show_policy_usage_in_use.png
 [20]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/SPT_HP_Update_SSD_to_fw_DM0T_show_policy_usage.png
 [21]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/HFP_DCA_HYP_227d_show_policy_usage_empty.png
 [22]: https://i0.wp.com/blog.vconsultants.be/wordpress/wp-content/uploads/2016/10/UCS_errors_empty.png