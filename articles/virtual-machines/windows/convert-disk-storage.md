---
title: Konvertera Azure managed disks-lagring från standard till premium, och vice versa | Microsoft Docs
description: Så här konverterar du Azure-hanterade diskar från standard till premium och vice versa med hjälp av Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 676adb05178097c1802c0ff5b3a5da1bbb7b188c
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978382"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Uppdatera lagringstypen för en hanterad disk

Azure Managed Disks finns tre alternativ för typ av lagring: [Premium SSD](../windows/premium-storage.md), [Standard SSD](../windows/disks-standard-ssd.md), och [Standard HDD](../windows/standard-storage.md). Du kan växla en hanterad disk mellan lagringstyper med minimal avbrottstid, baserat på dina behov. Växla mellan lagringstyper stöds inte för en ohanterad disk; men du kan enkelt [konvertera en ohanterad disk till en hanterad disk](convert-unmanaged-to-managed-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Förutsättningar

* Eftersom konverteringen kräver en omstart av den virtuella datorn (VM), bör du schemalägga migreringen av din disklagring under en schemalagd underhållstid. 
* Om du använder en ohanterad disk först [konvertera den till en hanterad disk](convert-unmanaged-to-managed-disks.md) så att du kan växla mellan lagringstyper. 
* Exemplen i den här artikeln kräver Azure PowerShell-Modulversion 6.0.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-az-ps) (Installera Azure PowerShell-modul). Kör [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) att skapa en anslutning till Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>Konvertera alla hanterade diskar på en virtuell dator från standard till premium

I följande exempel visas hur du växlar alla diskar på en virtuell dator från standard till premium storage. Om du vill använda premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet växlar också till en storlek som har stöd för premium storage:

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
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Konvertera en hanterad disk från standard till premium

Du kanske vill en blandning av standard och premium-diskar för att minska dina kostnader för utveckling/testning-arbetsbelastning. Uppgradera till premium-lagring gör du dessa diskar som behöver bättre prestanda. I följande exempel visas hur du växlar en enskild disk för en virtuell dator från standard till premium storage och vice versa. Om du vill använda premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet visar också hur du växlar till en storlek som har stöd för premium storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Konvertera en hanterad disk från standard HDD till standard SSD

I följande exempel visas hur du växlar en enskild disk för en virtuell dator från standard-Hårddisk till SSD som standard, och vice versa:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Nästa steg

Skapa en skrivskyddad kopia av en virtuell dator med hjälp av en [ögonblicksbild](snapshot-copy-managed-disk.md).

