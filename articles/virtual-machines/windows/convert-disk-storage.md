---
title: Konvertera hanterad disklagring mellan standard- och premium-SSD
description: Konvertera Azure-hanterade diskar från Standard till Premium eller Premium till Standard med hjälp av Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720953"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Uppdatera lagringstypen för en hanterad disk

Det finns fyra disktyper av Azure-hanterade diskar: Azure ultra SSDs (förhandsversion), premium SSD, standard SSD och standard HDD. Du kan växla mellan de tre GA-disktyperna (premium SSD, standard-SSD och standard HDD) baserat på dina prestandabehov. Du kan ännu inte byta från eller till en ultra SSD, måste du distribuera en ny.

Den här funktionen stöds inte för ohanterade diskar. Men du kan enkelt [konvertera en ohanterad disk till en hanterad disk](convert-unmanaged-to-managed-disks.md) för att kunna växla mellan disktyper.

 

## <a name="prerequisites"></a>Krav

* Eftersom konvertering kräver en omstart av den virtuella datorn (VM), bör du schemalägga migreringen av disklagringen under ett befintligt underhållsfönster.
* Om disken inte hanteras [konverteras den](convert-unmanaged-to-managed-disks.md) först till en hanterad disk så att du kan växla mellan lagringsalternativ.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Växla alla hanterade diskar i en virtuell dator mellan Premium och Standard

I det här exemplet visas hur du konverterar alla en virtuell dators diskar från Standard till Premium-lagring eller från Premium till Standard-lagring. Om du vill använda Premium-hanterade diskar måste den virtuella datorn använda en [vm-storlek](sizes.md) som stöder Premium-lagring. Det här exemplet växlar också till en storlek som stöder premiumlagring:

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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Växla enskilda hanterade diskar mellan Standard och Premium

För din utvecklings-/testarbetsbelastning kanske du vill ha en blandning av Standard- och Premium-diskar för att minska dina kostnader. Du kan välja att bara uppgradera de diskar som behöver bättre prestanda. I det här exemplet visas hur du konverterar en enda VM-disk från Standard till Premium-lagring eller från Premium till Standard-lagring. Om du vill använda Premium-hanterade diskar måste den virtuella datorn använda en [vm-storlek](sizes.md) som stöder Premium-lagring. I det här exemplet visas också hur du växlar till en storlek som stöder Premium-lagring:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Konvertera hanterade diskar från Standard till Premium i Azure-portalen

Följ de här stegen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över **virtuella datorer** i portalen.
3. Om den virtuella datorn inte stoppas väljer du **Stoppa** högst upp i **fönstret Översikt** för virtuella datorer och väntar på att den virtuella datorn ska stoppas.
3. Välj **Diskar** på menyn i fönstret för den virtuella datorn.
4. Markera den disk som du vill konvertera.
5. Välj **Konfiguration** på menyn.
6. Ändra **kontotypen** från **standard hdd** till **Premium SSD**.
7. Klicka på **Spara**och stäng diskfönstret.

Disktypkonverteringen är ögonblicklig. Du kan starta den virtuella datorn efter konverteringen.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Växla hanterade diskar mellan standard HDD och Standard SSD 

I det här exemplet visas hur du konverterar en enda VM-disk från standard HDD till Standard SSD eller från Standard SSD till Standard HDD:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Nästa steg

Gör en skrivskyddad kopia av en virtuell dator med hjälp av en [ögonblicksbild](snapshot-copy-managed-disk.md).
