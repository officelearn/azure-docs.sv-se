---
title: Expandera os-enheten för en Windows-virtuell dator i en Azure
description: Expandera storleken på OS-enheten för en virtuell dator med Azure Powershell i Resurshanterarens distributionsmodell.
author: mimckitt
manager: vashan
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: e69b041a2e4c8a0715adb6ab126a3aede42f7dde
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869694"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Utöka operativsystemenheten för en virtuell dator

När du skapar en ny virtuell dator (VM) i en resursgrupp genom att distribuera en avbildning från [Azure Marketplace](https://azure.microsoft.com/marketplace/)är standardoperativsystemet ofta 127 GB (vissa avbildningar har mindre diskstorlekar för operativsystem som standard). Även om det är möjligt att lägga till datadiskar i den virtuella datorn (hur många beror på vilken SKU som har valts) rekommenderas du att installera program och processorintensiva arbetsbelastningar på de här tilläggsdiskarna eftersom kunder ofta behöver expandera operativsystemenheten för att kunna hantera följande typ av scenarier:

- Stöd för äldre program som installerar komponenter på operativsystemenheten.
- Migrera en fysisk eller virtuell dator från lokal plats med större operativsystemenhet.


> [!IMPORTANT]
> Om du ändrar storlek på OS-disken på en virtuell Azure-dator måste den virtuella datorn hanteras.
>
> När du har expanderat diskarna måste du [utöka volymen i operativsystemet](#expand-the-volume-within-the-os) för att dra nytta av den större disken.
> 


 


## <a name="resize-a-managed-disk"></a>Ändra storlek på en hanterad disk

Öppna Powershell ISE eller Powershell-fönstret i administrativt läge och följ anvisningarna nedan:

1. Logga in på ditt Microsoft Azure-konto i resurshanteringsläge och välj din prenumeration på följande sätt:
   
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
5. Hämta en referens till den hanterade OS-disken. Ange storleken på den hanterade OS-disken till önskat värde och uppdatera disken enligt följande:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna är 2048 GB för OS-diskar. (Det är möjligt att expandera VHD blob utöver den storleken, men operativsystemet kommer bara att kunna arbeta med den första 2048 GB utrymme.)
   > 
   > 
6. Det kan ta några sekunder att uppdatera den virtuella datorn. När kommandot har körts klart startar du om den virtuella datorn på följande sätt:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Klart! Anslut till den virtuella datorn via RDP, öppna Datorhantering (eller Diskhantering) och expandera enheten med det nya utrymmet.

## <a name="resize-an-unmanaged-disk"></a>Ändra storlek på en ohanterlig disk

Öppna Powershell ISE eller Powershell-fönstret i administrativt läge och följ anvisningarna nedan:

1. Logga in på ditt Microsoft Azure-konto i resurshanteringsläge och välj din prenumeration på följande sätt:
   
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
5. Ange storleken på den ohanterat OS-disken till önskat värde och uppdatera den virtuella datorn enligt följande:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna är 2048 GB för OS-diskar. (Det är möjligt att expandera VHD blob utöver den storleken, men operativsystemet kommer bara att kunna arbeta med den första 2048 GB utrymme.)
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

Den här artikeln fokuserar främst på att utöka OS-disken för den virtuella datorn, men skriptet kan också användas för att expandera datadiskar som är anslutna till den virtuella datorn. Om du exempelvis vill expandera den första disken som är ansluten till den virtuella datorn, ersätter du objektet `OSDisk` i `StorageProfile` med matrisen `DataDisks` och använder ett numeriskt index för att hämta en referens till den första anslutna disken, på följande sätt:

**Hanterad disk**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Ohanterd disk**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



På samma sätt kan du referera till andra datadiskar som är kopplade till den virtuella datorn, antingen genom att använda ett index som visas ovan eller egenskapen **Name** på disken:


**Hanterad disk**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Ohanterd disk**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expandera volymen i operativsystemet

När du har expanderat disken för den virtuella datorn måste du gå in i operativsystemet och utöka volymen till att omfatta det nya utrymmet. Det finns flera metoder för att expandera en partition. Det här avsnittet omfattar anslutning av den virtuella datorn med en RDP-anslutning för att expandera partitionen med **DiskPart**.

1. Öppna en RDP-anslutning till den virtuella datorn.

2.  Öppna en kommandotolk och skriv **diskpart**.

2.  Skriv i `list volume` **diskpart-prompten** . Anteckna den volym som du vill utöka.

3.  Skriv i `select volume <volumenumber>` **diskpart-prompten** . Då markeras det *volymvolymnummer* som du vill utöka till angränsande, tomt utrymme på samma disk.

4.  Skriv i `extend [size=<size>]` **diskpart-prompten** . Detta utökar den valda volymen efter *storlek* i megabyte (MB).


## <a name="next-steps"></a>Nästa steg

Du kan också bifoga diskar med [Azure-portalen](attach-managed-disk-portal.md).
