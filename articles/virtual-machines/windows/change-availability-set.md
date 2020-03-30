---
title: Ändra en tillgänglighetsuppsättning för virtuella datorer
description: Lär dig hur du ändrar tillgänglighetsuppsättningen för din virtuella dator med Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: article
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 092dafff6622d3402322eb96d0fe4215e52e16b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964931"
---
# <a name="change-the-availability-set-for-a-vm"></a>Ändra tillgänglighetsuppsättning för en virtuell dator
I följande steg beskrivs hur du ändrar tillgänglighetsuppsättningen för en virtuell dator med Azure PowerShell. En virtuell dator kan bara läggas till i en tillgänglighetsuppsättning när den skapas. Om du vill ändra tillgänglighetsuppsättningen måste du ta bort och sedan återskapa den virtuella datorn. 

Den här artikeln gäller både virtuella Linux- och Windows-datorer.

Den här artikeln testades senast den 2019-02-12 med [Azure Cloud Shell](https://shell.azure.com/powershell) och Az [PowerShell-modulversionen](https://docs.microsoft.com/powershell/azure/install-az-ps) 1.2.0.


## <a name="change-the-availability-set"></a>Ändra tillgänglighetsuppsättningen 

Följande skript ger ett exempel på att samla in nödvändig information, ta bort den ursprungliga virtuella datorn och sedan återskapa den i en ny tillgänglighetsuppsättning.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
        {
            Add-AzVMNetworkInterface `
            -VM $newVM `
            -Id $nic.Id -Primary
            }
        else
            {
              Add-AzVMNetworkInterface `
              -VM $newVM `
              -Id $nic.Id 
                }
    }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Nästa steg

Lägg till ytterligare lagringsutrymme på den virtuella datorn genom att lägga till ytterligare en [datadisk](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

