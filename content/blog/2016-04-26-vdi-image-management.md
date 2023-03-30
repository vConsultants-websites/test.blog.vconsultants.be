---
title: VDI image management
author: Harold Preyers
type: post
date: -001-11-30T00:00:00+00:00
draft: true
url: /?p=44
categories:
  - Uncategorized

---
Copy VDI master image from one vCenter to another. It will create the necessary snapshots and update a pool with this VM and template. This is a script in progress.

<pre class="lang:ps decode:true">#Variables
#DC1
$DC1_vCenter = "DC1_vCenter"
$DC1_Template = "DC1_Template"
$DC1_Datastore = "DC1-Templates"
$DC1_VMXFile = "[$DC1_Datastore] $DC1_Template/$DC1_Template.vmx"
$DC1_ESXHost = "dc1-esxhost"
$DC1_VM_Folder = "Templates"
#DC2
$DC2_vCenter = "DC2_vCenter"
$DC2_Template = "DC2_Template"
$DC2_Datastore = "DC2-Templates"
$DC2_VMXFile = "[$DC2_Datastore] $DC2_Template/$DC2_Template.vmx"
$DC2_ESXHost = "dc2-esxhost"
$DC2_VM_Folder = "Templates"
#DC3
$DC3_vCenter = "Sources_vCenter"
$DC3_Template = "Source - Template"
$DC3_Datastore = "Source-Templates"
$DC3_VMXFile = "[$DC3_Datastore] $DC3_Template/$DC3_Template.vmx"
$DC3_ESXHost = "dc3-esxhost"
$DC3_VM_Folder = "Templates"

$Date = get-date -format dd/MM/yyyy

#Form
$title = "!! THE VM IS STILL RUNNING !!"
$message = "Are you finished customizing the image ?"
$yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", "Power Off the VM."
$no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", "Answering 'No' will keep the VM running and abort the script."
$options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

# Remove Template from SDC Infra vCenter
Connect-VIServer -Server $SDC_vCenter
Try {
 Get-VM -Server $SDC_vCenter -Name $SDC_Template -ErrorAction Stop | Out-Null
 Write-Host "VM exists !" -foregroundcolor red
 #Is the VM powered on?
 $PowerState = Get-VM -Name $SDC_Template | select PowerState
 If ($PowerState.PowerState -eq "PoweredOn"){
  $result = $host.ui.PromptForChoice($title, $message, $options, 0)
  switch ($result) {
   0 {
    Shutdown-VMGuest -Server $SDC_vCenter -VM $SDC_Template -Confirm:$false
    Do {
     $PowerState = Get-VM -Name $SDC_Template | select PowerState
     } While ($PowerState.PowerState -eq "PoweredOn")}
   1 {Exit}
  }
 }
 Write-Host "Cloning VM for DCA" -foregroundcolor red
 New-VM -Name $DCA_Template -VM $SDC_Template -Datastore $DCA_Datastore -VMHost $SDC_ESXHost -Notes $Date": DCA Template from $SDC_Template" -DiskStorageFormat Thin Write-Host "Removing from DCA vCenter" -foregroundcolor red
 Remove-VM -Server $SDC_vCenter -VM $DCA_Template -Confirm:$false
 Write-Host "Cloning VM for DCB" -foregroundcolor red
 New-VM -Name $DCB_Template -VM $SDC_Template -Datastore $DCB_Datastore -VMHost $SDC_ESXHost -Notes $Date": DCB Template from $SDC_Template" -DiskStorageFormat Thin
 Write-Host "Removing from DCB vCenter" -foregroundcolor red
 Remove-VM -Server $SDC_vCenter -VM $DCB_Template -Confirm:$false
}
Catch {
 Write-Host "VM doesn't exist" -foregroundcolor red
}

Disconnect-VIServer -Server $SDC_vCenter -Confirm:$false

# Add Template to DCA VDI vCenter
Connect-VIServer -Server $DCA_vCenter
Try {
 Get-VM -Server $DCA_vCenter -Name $DCA_Template -ErrorAction Stop | Out-Null
 Write-Host "VM exists, removing" -foregroundcolor red
 Remove-VM -Server $DCA_vCenter -VM $DCA_Template -Confirm:$false
 Write-Host "VM removed, re-adding to inventory" -foregroundcolor green   New-VM -VMFilePath $DCA_VMXFile -VMHost $DCA_ESXHost -Location $DCA_VM_Folder
 New-Snapshot -VM $DCA_Template -Name "VDI_Snapshot" -Description "$Date" -Confirm:$false
}
Catch {
 Write-Host "VM doesn't exist, adding to inventory" -foregroundcolor green
 Write-Host "$DCA_VMXFile" -foregroundcolor DarkYellow
 Write-Host "$DCA_ESXHost" -foregroundcolor DarkYellow
 Write-Host "$DCA_VM_Folder" -foregroundcolor DarkYellow
 New-VM -VMFilePath $DCA_VMXFile -VMHost $DCA_ESXHost -Location $DCA_VM_Folder
 New-Snapshot -VM $DCA_Template -Name "VDI_Snapshot" -Description "$Date" -Confirm:$false
}

Disconnect-VIServer -Server $DCA_vCenter -Force -Confirm:$false

# Add Template to DCB VDI vCenter
Connect-VIServer -Server $DCB_vCenter
Try {
 Get-VM -Server $DCB_vCenter -Name $DCB_Template -ErrorAction Stop | Out-Null
 Write-Host "VM exists, removing" -foregroundcolor red
 Remove-VM -Server $DCB_vCenter -VM $DCB_Template -Confirm:$false
 Write-Host "VM removed, re-adding to inventory" -foregroundcolor green   New-VM -VMFilePath $DCB_VMXFile -VMHost $DCB_ESXHost -Location $DCB_VM_Folder
 New-Snapshot -VM $DCB_Template -Name "VDI_Snapshot" -Description "$Date" -Confirm:$false
}
Catch {
 Write-Host "VM doesn't exist, adding to inventory" -foregroundcolor green
 Write-Host "$DCB_VMXFile" -foregroundcolor DarkYellow
 Write-Host "$DCB_ESXHost" -foregroundcolor DarkYellow
 Write-Host "$DCB_VM_Folder" -foregroundcolor DarkYellow
 New-VM -VMFilePath $DCB_VMXFile -VMHost $DCB_ESXHost -Location $DCB_VM_Folder
 New-Snapshot -VM $DCB_Template -Name "VDI_Snapshot" -Description "$Date" -Confirm:$false
}
Disconnect-VIServer -Server $DCB_vCenter -Confirm:$false

$s = New-PSSession -computerName DCB-SRVCONCAD01
Invoke-Command -Session $s -Scriptblock {Add-PSSnapin vmware.view.broker}
Invoke-Command -Session $s -Scriptblock {Update-AutomaticLinkedClonePool -pool_id DCB_VDI_CIC_WVL -ParentVmPath /VDI_DCB/vm/Templates/Test_DCB -ParentSnapshotPath /VDI_Snapshot}
Remove-PSSession $s</pre>

&nbsp;