---
title: Anslut en datadisk till en Windows-dator i Azure med hjälp av PowerShell | Microsoft Docs
description: Så här kopplar du en ny eller befintlig datadisk till en Windows virtuell dator med hjälp av PowerShell med Resource Manager-distributionsmodellen.
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
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1abc3fc18de3e9c1751c01c984e15ae44f25d5af
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766141"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Anslut en datadisk till en virtuell Windows-dator med PowerShell

Den här artikeln visar hur du kopplar både nya och befintliga diskar till en Windows-dator med hjälp av PowerShell. 

Granska först de här tipsen:

* Storleken på den virtuella datorn styr hur många datadiskar som du kan koppla. Mer information finns i [storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Om du vill använda premium SSD, behöver du en [premium storage-aktiverade VM-typ](sizes-memory.md), till exempel DS-serien eller GS-serien virtuella datorn.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Lägg till en tom datadisk till en virtuell dator

Det här exemplet visar hur du lägger till en tom datadisk till en befintlig virtuell dator.

### <a name="using-managed-disks"></a>Använder hanterade diskar

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Använda hanterade diskar i en Tillgänglighetszon

Du kan skapa en disk i en Tillgänglighetszon med [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) med den `-Zone` parametern. I följande exempel skapas en disk i zonen *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Initiera disken

När du lägger till en tom disk måste du initiera den. Du kan logga in på en virtuell dator och använda Diskhantering för att initiera disken. Om du har aktiverat [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) och ett certifikat på den virtuella datorn när du skapade det. Du kan använda fjärr-PowerShell för att initiera disken. Du kan också använda ett anpassat skripttillägg:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Skriptfilen kan innehålla kod för att initiera diskar, till exempel:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Koppla en befintlig datadisk till en virtuell dator

Du kan koppla en befintlig hanterad disk till en virtuell dator som en datadisk.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Nästa steg

Skapa en [ögonblicksbild](snapshot-copy-managed-disk.md).
