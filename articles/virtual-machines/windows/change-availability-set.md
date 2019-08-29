---
title: Ändra tillgänglighets uppsättning för virtuella datorer | Microsoft Docs
description: Lär dig hur du ändrar tillgänglighets uppsättningen för dina virtuella datorer med hjälp av Azure PowerShell och distributions modellen för Resource Manager.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/12/2019
ms.author: cynthn
ms.openlocfilehash: 0a91a80c18b04e257daa9a42fd7933351fe3a35c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080136"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Ändra tillgänglighets uppsättning för en virtuell Windows-dator
Följande steg beskriver hur du ändrar tillgänglighets uppsättningen för en virtuell dator med hjälp av Azure PowerShell. En virtuell dator kan bara läggas till i en tillgänglighets uppsättning när den skapas. Om du vill ändra tillgänglighets uppsättningen måste du ta bort och sedan återskapa den virtuella datorn. 

Den här artikeln testades senast 2/12/2019 med hjälp av [Azure Cloud Shell](https://shell.azure.com/powershell) och [AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) -modulens version 1.2.0.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="change-the-availability-set"></a>Ändra tillgänglighets uppsättning 

Följande skript innehåller ett exempel på hur du samlar in nödvändig information, tar bort den ursprungliga virtuella datorn och sedan återskapar den i en ny tillgänglighets uppsättning.

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

# Create the basic configuration for the replacement VM
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
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

Lägg till ytterligare lagrings utrymme i den virtuella datorn [](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)genom att lägga till ytterligare en datadisk.

