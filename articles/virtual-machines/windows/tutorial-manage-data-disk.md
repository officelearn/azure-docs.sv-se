---
title: Hantera Azure-diskarna med Azure PowerShell | Microsoft Docs
description: "Självstudiekurs – hantera Azure-diskarna med Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 58c8ba2682cc9cc8f2089d2a70cc95a03079832e
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="manage-azure-disks-with-powershell"></a>Hantera Azure-diskar med PowerShell

Virtuella Azure-datorer använder diskar för att lagra virtuella datorer operativsystem, program och data. När du skapar en virtuell dator är det viktigt att välja en diskstorleken och konfiguration som är lämpligt att förväntade arbetsbelastningen. Den här kursen ingår distribuerar och hanterar Virtuella diskar. Du lär dig mer om:

> [!div class="checklist"]
> * OS-diskar och tillfällig diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Koppla och förbereda datadiskar

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="default-azure-disks"></a>Standard Azure-diskar

När en virtuell Azure-dator har skapats är automatiskt två diskar kopplade till den virtuella datorn. 

**Operativsystemdisken** -drift systemdiskar kan storleksändras upp till 4 terabyte och är värd för operativsystemet för virtuella datorer.  OS-disken har tilldelats en enhetsbokstav för *c:* som standard. Disken cachelagring konfigurationen av OS-disken har optimerats för OS-prestanda. OS-disk **bör inte** värd för program eller data. Använd en datadisk, vilket beskrivs senare i den här artikeln för program och data.

**Diskutrymme** -tillfälliga diskar använder ett SSD-enhet som finns på samma Azure-värd som den virtuella datorn. Temporär diskar har hög performant och kan användas för åtgärder som till exempel temporär databearbetning. Om den virtuella datorn flyttas till en ny värd bort data som lagrats på en tillfällig disk. Storleken på den tillfälliga disken bestäms av VM-storlek. Tillfällig diskar tilldelas en enhetsbeteckning för *d:* som standard.

### <a name="temporary-disk-sizes"></a>Tillfällig diskstorlekar

| Typ | Storlek på virtuell dator | Maxstorlek för temporär disk (GB) |
|----|----|----|
| [Generellt syfte](sizes-general.md) | A och D-serien | 800 |
| [Beräkningsoptimerad](sizes-compute.md) | F-serien | 800 |
| [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md) | D och G serien | 6144 |
| [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md) | Serie L | 5630 |
| [GPU](sizes-gpu.md) | N serien | 1440 |
| [Hög prestanda](sizes-hpc.md) | A och H serien | 2000 |

## <a name="azure-data-disks"></a>Azure datadiskar

Ytterligare datadiskar kan läggas till för att installera program och lagra data. Datadiskar som ska användas i en situation där varaktiga och känslig datalagring önskas. Varje datadisk har en maximal kapacitet för 1 terabyte. Storleken på den virtuella datorn avgör hur många datadiskar som kan kopplas till en virtuell dator. För varje VM-vCPU, kan två diskar kopplas. 

### <a name="max-data-disks-per-vm"></a>Maximalt antal datadiskar per VM

| Typ | Storlek på virtuell dator | Maximalt antal datadiskar per VM |
|----|----|----|
| [Generellt syfte](sizes-general.md) | A och D-serien | 32 |
| [Beräkningsoptimerad](sizes-compute.md) | F-serien | 32 |
| [Minnesoptimerad](../virtual-machines-windows-sizes-memory.md) | D och G serien | 64 |
| [Lagringsoptimerad](../virtual-machines-windows-sizes-storage.md) | Serie L | 64 |
| [GPU](sizes-gpu.md) | N serien | 48 |
| [Hög prestanda](sizes-hpc.md) | A och H serien | 32 |

## <a name="vm-disk-types"></a>VM-disktyper

Azure tillhandahåller två typer av disk.

### <a name="standard-disk"></a>Standard disk

Standard Storage stöds av hårddiskar och levererar kostnadseffektiv lagring samtidigt som det är högpresterande. Standarddiskar är idealiskt för ett kostnadseffektivt dev och testa arbetsbelastning.

### <a name="premium-disk"></a>Premium-disk

Premiumdiskar backas upp av SSD-baserad hög prestanda, låg latens disk. Perfekt för virtuella datorer som kör produktion arbetsbelastning. Premium-lagring stöder DS-serien, DSv2-serien GS-serien och FS-serien virtuella datorer. Premiumdiskar kommer i fem typer (P10, P20, P30, P40, p 50) och storleken på disken fastställer typ av disk. När du väljer, avrundat diskstorleken värdet till nästa typen. Till exempel om storleken är lägre än 128 GB disktyp är P10, mellan 129 och 512 P20 512 för P30, P40 för 2TB och p 50 4TB. 

### <a name="premium-disk-performance"></a>Premium-diskprestanda

|Premium-lagring disktyp | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Diskens storlek (avrunda uppåt) | 128 GB | 512 GB | 1 024 GB (1 TB) |
| IOPS per disk | 500 | 2,300 | 5,000 |
Dataflöde per disk | 100 MB/s | 150 MB/s | 200 MB/s |

När tabellen ovan identifierar högsta IOPS per disk, kan en högre nivå av prestanda uppnås genom striping flera datadiskar. Till exempel kan 64 diskar kopplas till Standard_GS5 VM. Om var och en av dessa diskar har storlek som en P30 kan högst 80 000 IOPS uppnås. Detaljerad information om högsta IOPS per VM finns [VM typer och storlekar](./sizes.md).

## <a name="create-and-attach-disks"></a>Skapa och koppla diskar

Du måste ha en befintlig virtuell dator för att slutföra exemplet i den här självstudiekursen. Om det behövs, detta [skriptexempel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) kan skapa en åt dig. När gå igenom kursen, ersätter namn resursgrupp och VM där det behövs.

Skapa den första konfigurationen med [ny AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). I följande exempel konfigureras en disk som är 128 GB i storlek.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig -Location EastUS -CreateOption Empty -DiskSizeGB 128
```

Skapa datadisk med den [ny AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) kommando.

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myDataDisk -Disk $diskConfig
```

Hämta den virtuella dator som du vill lägga till datadisk för med den [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) kommando.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Lägg till datadisk i konfigurationen av virtuella datorn med den [Lägg till AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk) kommando.

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk -VM $vm -Name myDataDisk -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1
```

Uppdatera den virtuella datorn med den [uppdatering AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk) kommando.

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="prepare-data-disks"></a>Förbereda datadiskar

När en disk har kopplats till den virtuella datorn, måste operativsystemet konfigureras för att använda disken. I följande exempel visas hur du konfigurerar den första disk som lagts till i den virtuella datorn manuellt. Den här processen kan också automatiseras med hjälp av den [tillägget för anpassat skript](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Manuell konfiguration

Skapa en RDP-anslutning med den virtuella datorn. Öppna PowerShell och kör det här skriptet.

```azurepowershell-interactive
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig om Virtuella diskar ämnen som:

> [!div class="checklist"]
> * OS-diskar och tillfällig diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Koppla och förbereda datadiskar

Gå vidare till nästa kurs mer information om hur du automatiserar VM-konfiguration.

> [!div class="nextstepaction"]
> [Automatisera konfiguration av virtuella datorer](./tutorial-automate-vm-deployment.md)
