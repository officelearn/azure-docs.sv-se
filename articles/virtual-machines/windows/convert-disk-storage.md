---
title: Konvertera Azure managed disks-lagring från Standard till Premium- eller Premium till Standard | Microsoft Docs
description: Så här konverterar du Azure hanterade diskar från Standard till Premium- eller Premium till Standard med hjälp av Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd893c68f2e9cac713e09dd0bdafb7f277ae889
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707527"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Uppdatera lagringstypen för en hanterad disk

Det finns fyra alternativ för Azure hanterade diskar: Azure Ultra disklagring-, Premium SSD-, Standard SSD- och Standard HDD. Du kan växla mellan dessa lagringstyper baserat på dina behov med lite avbrott. Den här funktionen stöds inte för ohanterade diskar. Men du kan enkelt [konvertera en ohanterad disk till en hanterad disk](convert-unmanaged-to-managed-disks.md) för att kunna växla mellan disktyper.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Eftersom konverteringen kräver en omstart av den virtuella datorn (VM), bör du schemalägga migreringen av din disklagring under en schemalagd underhållstid.
* Om disken inte är hanterad, först [konvertera den till en hanterad disk](convert-unmanaged-to-managed-disks.md) så att du kan växla mellan lagringsalternativ.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Växla alla hanterade diskar på en virtuell dator mellan Premium och Standard

Det här exemplet visar hur du konverterar alla diskar på en virtuell dator från Standard till Premium storage eller från Premium till Standard storage. Om du vill använda Premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet växlar också till en storlek som har stöd för premium storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Växla enskilda hanterade diskar mellan Standard och Premium

Du kanske vill en blandning av Standard och Premium-diskar för att minska kostnaderna för utveckling/testning-arbetsbelastning. Du kan välja att uppgradera de diskar som behöver bättre prestanda. Det här exemplet visar hur du konverterar en enskild datordisk för virtuell från Standard till Premium storage eller från Premium till Standard storage. Om du vill använda Premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet visar också hur du växlar till en storlek som har stöd för Premium storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Konvertera hanterade diskar från Standard till Premium i Azure portal

Följ de här stegen:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj den virtuella datorn från listan över **virtuella datorer** i portalen.
3. Om den virtuella datorn inte är stoppad väljer **stoppa** överst i VM **översikt** fönstret och vänta tills den virtuella datorn att stoppa.
3. I fönstret för den virtuella datorn, väljer **diskar** på menyn.
4. Välj den disk som du vill konvertera.
5. Välj **Configuration** på menyn.
6. Ändra den **kontotyp** från **Standard HDD** till **Premium SSD**.
7. Klicka på **spara**, och Stäng fönstret disk.

Typkonvertering disk är omedelbart. Du kan starta om den virtuella datorn efter konverteringen.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Växla hanterade diskar mellan Standard HDD- och Standard SSD 

Det här exemplet visar hur du konverterar en enskild datordisk för virtuell från Standard Hårddisk till Standard SSD eller från Standard SSD till Standard HDD:

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
