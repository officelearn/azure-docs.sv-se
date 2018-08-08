---
title: Konvertera Azure managed disks-lagring från standard till premium, och vice versa | Microsoft Docs
description: Så här konverterar du Azure-hanterade diskar från standard till premium och vice versa med hjälp av Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 7748cf41bb97e0136d7b619debcb60d460df5d8b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596587"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Konvertera Azure managed disks-lagring från standard till premium, och vice versa

Hanterade diskar erbjuder tre lagringsalternativ: [Premium SSD](../windows/premium-storage.md), Standard SSD(Preview) och [Standard HDD](../windows/standard-storage.md). Det kan du enkelt växla mellan alternativen med minimal avbrottstid utifrån dina behov. Det finns inte stöd för ohanterade diskar. Men du kan enkelt [konvertera till managed disks](convert-unmanaged-to-managed-disks.md) enkelt växla mellan disktyper.

Den här artikeln visar hur du konverterar hanterade diskar från standard till premium och vice versa med hjälp av Azure PowerShell. Om du behöver installera eller uppgradera den kan du läsa [installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Innan du börjar

* Konverteringen kräver en omstart av den virtuella datorn, så du bör schemalägga migreringen av din disklagring under en schemalagd underhållstid. 
* Om du använder ohanterade diskar först [konvertera till managed disks](convert-unmanaged-to-managed-disks.md) du använder den här artikeln för att växla mellan lagringsalternativ. 
* Den här artikeln kräver Azure PowerShell-Modulversion 6.0.0 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Du måste också köra `Connect-AzureRmAccount` för att upprätta en anslutning till Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Konvertera alla hanterade diskar på en virtuell dator från standard till premium, och vice versa

I följande exempel visas hur du växlar alla diskar på en virtuell dator från standard till premium storage. Om du vill använda premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet växlar också till en storlek som stöder premiumlagring.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Konvertera en hanterad disk från standard till premium, och vice versa

Du kanske vill ha blandning av standard och premium-diskar för att minska dina kostnader för utveckling/testning-arbetsbelastning. Du kan göra det genom att uppgradera till premium storage kan endast diskar som behöver bättre prestanda. I följande exempel visas hur du växlar en enskild disk för en virtuell dator från standard till premium storage och vice versa. Om du vill använda premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet växlar också till en storlek som stöder premiumlagring.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Konvertera en hanterad disk från standard HDD till standard SSD och vice versa

I följande exempel visas hur du växlar en enskild disk för en virtuell dator från standard-Hårddisk till SSD som standard, och vice versa.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Nästa steg

Ta en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicksbilder](snapshot-copy-managed-disk.md).

