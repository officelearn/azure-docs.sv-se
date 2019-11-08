---
title: Expandera OS-enheten för en virtuell Windows-dator i en Azure | Microsoft Docs
description: Expandera storleken på operativ system enheten för en virtuell dator med hjälp av Azure PowerShell i distributions modellen för Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 12fa8cb09a9864b49c9368462ae3d5ca1d88f2c9
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749407"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Expandera OS-enheten för en virtuell dator

När du skapar en ny virtuell dator i en resurs grupp genom att distribuera en avbildning från [Azure Marketplace](https://azure.microsoft.com/marketplace/), är standard operativ system enheten ofta 127 GB (vissa avbildningar har mindre disk storlekar för operativ system som standard). Även om det är möjligt att lägga till datadiskar i den virtuella datorn (hur många beror på vilken SKU som har valts) rekommenderas du att installera program och processorintensiva arbetsbelastningar på de här tilläggsdiskarna eftersom kunder ofta behöver expandera operativsystemenheten för att kunna hantera följande typ av scenarier:

- Stöd för äldre program som installerar komponenter på operativsystemenheten.
- Migrera en fysisk eller virtuell dator från lokal plats med större operativsystemenhet.


> [!IMPORTANT]
> Att ändra storlek på operativ system disken för en virtuell Azure-dator kräver att den virtuella datorn frigörs.
>
> När du har expanderat diskarna måste du [expandera volymen i operativ systemet](#expand-the-volume-within-the-os) för att dra nytta av den större disken.
> 


 


## <a name="resize-a-managed-disk"></a>Ändra storlek på en hanterad disk

Öppna Powershell ISE eller Powershell-fönstret i administrativt läge och följ anvisningarna nedan:

1. Logga in på ditt Microsoft Azure-konto i resurs hanterings läge och välj din prenumeration på följande sätt:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ange namn på resursgrupp och virtuell dator på följande sätt:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Hämta en referens till din virtuella dator på följande sätt:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Stoppa den virtuella datorn innan du ändrar storlek på disken på följande sätt:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Hämta en referens till den hanterade OS-disken. Ställ in storleken på den hanterade OS-disken på önskat värde och uppdatera disken enligt följande:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna antalet är 2048 GB för OS-diskar. (Det går att expandera VHD-blobben utöver den storleken, men operativ systemet kommer bara att kunna arbeta med de första 2048 GB utrymme.)
   > 
   > 
6. Det kan ta några sekunder att uppdatera den virtuella datorn. När kommandot har körts klart startar du om den virtuella datorn på följande sätt:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Klart! Anslut till den virtuella datorn via RDP, öppna Datorhantering (eller Diskhantering) och expandera enheten med det nya utrymmet.

## <a name="resize-an-unmanaged-disk"></a>Ändra storlek på en ohanterad disk

Öppna Powershell ISE eller Powershell-fönstret i administrativt läge och följ anvisningarna nedan:

1. Logga in på ditt Microsoft Azure-konto i resurs hanterings läge och välj din prenumeration på följande sätt:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ange namn på resursgrupp och virtuell dator på följande sätt:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Hämta en referens till din virtuella dator på följande sätt:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Stoppa den virtuella datorn innan du ändrar storlek på disken på följande sätt:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Ange storleken på den ohanterade OS-disken till önskat värde och uppdatera den virtuella datorn enligt följande:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna antalet är 2048 GB för OS-diskar. (Det går att expandera VHD-blobben utöver den storleken, men operativ systemet kommer bara att kunna arbeta med de första 2048 GB utrymme.)
   > 
   > 
   
6. Det kan ta några sekunder att uppdatera den virtuella datorn. När kommandot har körts klart startar du om den virtuella datorn på följande sätt:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Skript för OS-disk

Nedan visas det fullständiga skriptet för din referens för både hanterade och ohanterade diskar:


**Hanterade diskar**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Ohanterade diskar**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Ändra storlek på data diskar

Den här artikeln fokuserar främst på att utöka den virtuella datorns OS-disk, men skriptet kan också användas för att utöka data diskarna som är anslutna till den virtuella datorn. Om du exempelvis vill expandera den första disken som är ansluten till den virtuella datorn, ersätter du objektet `OSDisk` i `StorageProfile` med matrisen `DataDisks` och använder ett numeriskt index för att hämta en referens till den första anslutna disken, på följande sätt:

**Hanterad disk**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Ohanterad disk**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



På samma sätt kan du referera till andra data diskar som är anslutna till den virtuella datorn, antingen genom att använda ett index som visas ovan eller på diskens **namn** egenskap:


**Hanterad disk**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Ohanterad disk**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expandera volymen i operativ systemet

När du har expanderat disken för den virtuella datorn måste du gå till operativ systemet och expandera volymen för att omfatta det nya utrymmet. Det finns flera metoder för att expandera en partition. I det här avsnittet beskrivs hur du ansluter den virtuella datorn via en RDP-anslutning för att expandera partitionen med **DiskPart**.

1. Öppna en RDP-anslutning till den virtuella datorn.

2.  Öppna en kommando tolk och skriv **DiskPart**.

2.  Skriv `list volume`i **DiskPart** -prompten. Anteckna den volym som du vill utöka.

3.  Skriv `select volume <volumenumber>`i **DiskPart** -prompten. Detta väljer den volym *volumenumber* som du vill utöka till ett sammanhängande, tomt utrymme på samma disk.

4.  Skriv `extend [size=<size>]`i **DiskPart** -prompten. Detta utökar den valda volymen efter *storlek* i megabyte (MB).


## <a name="next-steps"></a>Nästa steg

Du kan också ansluta diskar med hjälp av [Azure Portal](attach-managed-disk-portal.md).
