---
title: Expandera operativsystemenheten för en virtuell Windows-dator i Azure | Microsoft Docs
description: Öka storleken på operativsystemenheten för en virtuell dator med Azure Powershell i Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: bd863a8ddd9e2277b628673d2146efd8c458c319
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979504"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Hur du expandera operativsystemenheten för en virtuell dator

När du skapar en ny virtuell dator (VM) i en resursgrupp genom att distribuera en avbildning från [Azure Marketplace](https://azure.microsoft.com/marketplace/), är ofta Operativsystemets standarddisk 127 GB (vissa bilder har mindre OS-diskstorlekar som standard). Även om det är möjligt att lägga till datadiskar i den virtuella datorn (hur många beror på vilken SKU som har valts) rekommenderas du att installera program och processorintensiva arbetsbelastningar på de här tilläggsdiskarna eftersom kunder ofta behöver expandera operativsystemenheten för att kunna hantera följande typ av scenarier:

- Stöd för äldre program som installerar komponenter på operativsystemenheten.
- Migrera en fysisk eller virtuell dator från lokal plats med större operativsystemenhet.


> [!IMPORTANT]
> Ändra storlek på Operativsystemdisken för en Azure-dator kommer den att starta om.
>
> När du expanderar diskarna, måste du [utöka volymen i Operativsystemet](#expand-the-volume-within-the-os) att dra nytta av större disk.
> 


[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]


## <a name="resize-a-managed-disk"></a>Ändra storlek på en hanterad disk

Öppna Powershell ISE eller Powershell-fönstret i administrativt läge och följ anvisningarna nedan:

1. Logga in på Microsoft Azure-kontot i resurshanteringsläge och välj din prenumeration på följande sätt:
   
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
5. Hämta en referens till den hantera OS-disken. Ange storleken på den hantera OS-disken till önskat värde och uppdatera disken på följande sätt:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna är 2 048 GB för OS-diskar. (Det är möjligt att expandera VHD-bloben utöver den storleken, men Operativsystemet kommer bara att kunna arbeta med de första 2 048 GB utrymme.)
   > 
   > 
6. Det kan ta några sekunder att uppdatera den virtuella datorn. När kommandot har körts klart startar du om den virtuella datorn på följande sätt:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Klart! Anslut till den virtuella datorn via RDP, öppna Datorhantering (eller Diskhantering) och expandera enheten med det nya utrymmet.

## <a name="resize-an-unmanaged-disk"></a>Ändra storlek på en ohanterad disk

Öppna Powershell ISE eller Powershell-fönstret i administrativt läge och följ anvisningarna nedan:

1. Logga in på Microsoft Azure-kontot i resurshanteringsläge och välj din prenumeration på följande sätt:
   
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
5. Ange storleken på ohanterade OS-disken till önskat värde och uppdatera den virtuella datorn på följande sätt:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna är 2 048 GB för OS-diskar. (Det är möjligt att expandera VHD-bloben utöver den storleken, men Operativsystemet kommer bara att kunna arbeta med de första 2 048 GB utrymme.)
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

## <a name="resizing-data-disks"></a>Ändra storlek på datadiskar

Den här artikeln fokuserar främst på att utöka den virtuella datorn OS-disk, men skriptet kan också användas för att expandera de diskar som är anslutna till den virtuella datorn. Om du exempelvis vill expandera den första disken som är ansluten till den virtuella datorn, ersätter du objektet `OSDisk` i `StorageProfile` med matrisen `DataDisks` och använder ett numeriskt index för att hämta en referens till den första anslutna disken, på följande sätt:

**Hanterad disk**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Ohanterad disk**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



På samma sätt kan du referera till andra diskar som är anslutna till den virtuella datorn, antingen med hjälp av ett index som ovan eller **namn** egenskapen för disken:


**Hanterad disk**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Ohanterad disk**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Utöka volymen i Operativsystemet

När du har expanderat disken för den virtuella datorn, måste du gå till Operativsystemet och utöka volymen så att den omfattar det nya utrymmet. Det finns flera metoder för att expandera en partition. Det här avsnittet beskriver ansluta den virtuella datorn med hjälp av en RDP-anslutning för att expandera en partition med hjälp av **DiskPart**.

1. Öppna en RDP-anslutning till den virtuella datorn.

2.  Öppna en kommandotolk och skriv **diskpart**.

2.  På den **DISKPART** anger `list volume`. Anteckna den volym som du vill utöka.

3.  På den **DISKPART** anger `select volume <volumenumber>`. Då markeras volymen *volumenumber* som du vill utöka till angränsande, tomt utrymme på samma disk.

4.  På den **DISKPART** anger `extend [size=<size>]`. Detta utökar den valda volymen av *storlek* i megabyte (MB).


## <a name="next-steps"></a>Nästa steg

Du kan även ansluta diskar med hjälp av den [Azure-portalen](attach-managed-disk-portal.md).
