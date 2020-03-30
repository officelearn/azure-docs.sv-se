---
title: Koppla en datadisk till en Windows VM i Azure med powershell
description: Bifoga en ny eller befintlig datadisk till en Windows VM med PowerShell med Resurshanterarens distributionsmodell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ce995a84d2290845e83416caf9c8b0004242eed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267760"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Koppla en datadisk till en Windows VM med PowerShell

Den här artikeln visar hur du ansluter både nya och befintliga diskar till en virtuell Windows-dator med hjälp av PowerShell. 

Först, granska dessa tips:

* Storleken på den virtuella datorn styr hur många datadiskar du kan koppla. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Om du vill använda premium-SSD-enheter behöver du en [premiumlagringsaktiverad vm-typ,](sizes-memory.md)till exempel den virtuella datorn i DS-serien eller GS-serien.

Den här artikeln använder PowerShell i [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), som ständigt uppdateras till den senaste versionen. Om du vill öppna Cloud Shell väljer du **Prova det** överst i alla kodblock.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Lägga till en tom datadisk på en virtuell dator

I det här exemplet visas hur du lägger till en tom datadisk på en befintlig virtuell dator.

### <a name="using-managed-disks"></a>Använda hanterade diskar

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

### <a name="using-managed-disks-in-an-availability-zone"></a>Använda hanterade diskar i en tillgänglighetszon

Om du vill skapa en disk i en tillgänglighetszon `-Zone` använder du [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) med parametern. I följande exempel skapas en disk i zon *1*.

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

När du har lagt till en tom disk måste du initiera den. Om du vill initiera disken kan du logga in på en virtuell dator och använda diskhantering. Om du har aktiverat [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) och ett certifikat på den virtuella datorn när du skapade den kan du använda fjärr-PowerShell för att initiera disken. Du kan också använda ett anpassat skripttillägg:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Skriptfilen kan innehålla kod för att initiera diskarna, till exempel:

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

Du kan också distribuera hanterade diskar med hjälp av mallar. Mer information finns [i Använda hanterade diskar i Azure Resource Manager-mallar](using-managed-disks-template-deployments.md) eller [snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) för distribution av flera datadiskar.
