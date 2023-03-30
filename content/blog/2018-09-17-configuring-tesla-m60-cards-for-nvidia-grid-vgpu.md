---
title: Configuring Tesla M60 cards for NVIDIA GRID vGPU
author: Harold Preyers
type: post
date: 2018-09-17T09:35:19+00:00
url: /configuring-tesla-m60-cards-for-nvidia-grid-vgpu/
featured_image: https://blog.vconsultants.be/wordpress/wp-content/uploads/2018/09/tesla-3-quater.png
et_enqueued_post_fonts:
  - 'a:3:{s:6:"family";a:4:{s:13:"et-gf-allerta";s:15:"Allerta:regular";s:11:"et-gf-alice";s:13:"Alice:regular";s:16:"et-gf-montserrat";s:137:"Montserrat:100,200,300,regular,500,600,700,800,900,100italic,200italic,300italic,italic,500italic,600italic,700italic,800italic,900italic";s:19:"et-gf-alegreya-sans";s:112:"Alegreya+Sans:100,100italic,300,300italic,regular,italic,500,500italic,700,700italic,800,800italic,900,900italic";}s:6:"subset";a:7:{i:0;s:5:"latin";i:1;s:8:"cyrillic";i:2;s:12:"cyrillic-ext";i:3;s:9:"latin-ext";i:4;s:10:"vietnamese";i:5;s:5:"greek";i:6;s:9:"greek-ext";}s:9:"cache_key";s:72:"{"gph":-1,"divi":"4.20.2","wp":"6.1.1","enable_all_character_sets":"on"}";}'
categories:
  - ESXi
  - Horizon
  - NVIDIA GRID
  - VMware
  - vSphere

---
\[et\_pb\_section bb\_built=&#8221;1&#8243; inner\_shadow=&#8221;on&#8221; fullwidth=&#8221;on&#8221; \_builder\_version=&#8221;3.14&#8243; background\_color=&#8221;#2ea3f2&#8243; use\_background\_color\_gradient=&#8221;on&#8221; background\_color\_gradient\_start=&#8221;#2ea3f2&#8243; background\_color\_gradient\_end=&#8221;#2e6df4&#8243; background\_color\_gradient\_direction=&#8221;330deg&#8221; border\_radii=&#8221;on|1px|1px|1px|1px&#8221; top\_divider\_style=&#8221;clouds&#8221; top\_divider\_color=&#8221;rgba(0,226,247,0)&#8221; bottom\_divider\_height=&#8221;50px&#8221; bottom\_divider\_style=&#8221;asymmetric3&#8243; next\_background\_color=&#8221;#ffffff&#8221;\]\[et\_pb\_fullwidth\_post\_title \_builder\_version=&#8221;3.14&#8243; text\_orientation=&#8221;center&#8221; text\_color=&#8221;light&#8221; featured\_image=&#8221;off&#8221; /\]\[/et\_pb\_section\]\[et\_pb\_section bb\_built=&#8221;1&#8243; fullwidth=&#8221;off&#8221; specialty=&#8221;off&#8221; prev\_background\_color=&#8221;#2ea3f2&#8243;\]\[et\_pb\_row\]\[et\_pb\_column type=&#8221;4\_4&#8243;\][et\_pb\_text \_builder_version=&#8221;3.14&#8243;]

There are a couple of steps which need to be taken to configure the Tesla M60 cards with NVIDIA GRID VGPU in a vSphere / Horizon environment. I have listed them here quick and dirty. They are an extract of the NVIDIA <a href="https://docs.nvidia.com/grid/6.0/grid-vgpu-user-guide/index.html" target="_blank" rel="noopener">Virtual GPU Software User Guide</a>.

  * On the host(s): 
      * Install the vib 
          * esxcli software vib install -v directory/NVIDIA-vGPUVMware\_ESXi\_6.0\_Host\_Driver_390.72-1OEM.600.0.0.2159203.vib
      * Reboot the host(s)
      * Check if the module is loaded 
          * vmkload_mod -l | grep nvidia
      * Run the nvidia-smi command to verify the correct communictation with the device
      * Configuring Suspend and Resume for VMware vSphere 
          * esxcli system module parameters set -m nvidia -p &#8220;NVreg_RegistryDwords=RMEnableVgpuMigration=1&#8221;
      * Reboot the host
      * Confirm that suspend and resume is configured 
          * dmesg | grep NVRM
      * Check that the default graphics type is set to shared direct
      * If the graphics type were not set to shared direct, execute the following commands to stop and start the xorg and nv-hostengine services 
          * /etc/init.d/xorg stop
          * nv-hostengine -t
          * nv-hostengine -d
          * /etc/init.d/xorg start

  * On the VM / Parent VM: 
      * Configure the VM, beware that once the vGPU is configured that the console of the VM will not be visible/accessible through the vSphere Client. An alternate access method should already be foreseen
      * Edit the VM configuration to add a shared pci device, verify that NVIDIA GRID vGPU is selected
      * Choose the vGPU profile  
        more info on the profiles can be found here under section &#8216;1.4.1 Virtual GPU Types&#8217;: <a href="https://docs.nvidia.com/grid/6.0/grid-vgpu-user-guide/index.html" target="_blank" rel="noopener">https://docs.nvidia.com/grid/6.0/grid-vgpu-user-guide/index.html</a>
      * Reserve all guest memory

  * On the Horizon pool 
      * Configure the pool to use the NVIDIA GRID vGPU as 3D Renderer

\[/et\_pb\_text\]\[/et\_pb\_column\]\[/et\_pb\_row\]\[/et\_pb\_section\]