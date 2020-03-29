---
title: Skapa en ögonblicksbild av en virtuell hårddisk i Azure
description: Lär dig hur du skapar en kopia av en Virtuell Azure-dator som ska användas som säkerhetskopiering eller felsökningsproblem.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bc74a3eea1f99de6080788d6f3fddcac823092dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370909"
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk (VHD). Du kan ta en ögonblicksbild av en virtuell hårddisk eller datadisk som ska användas som säkerhetskopiering eller felsöka problem med virtuella datorer .You can take a snapshot of an OS or data disk VHD to use as a backup, or to troubleshoot virtual machine (VM) issues.

Om du ska använda ögonblicksbilden för att skapa en ny virtuell dator rekommenderar vi att du stänger av den virtuella datorn på ett rent sätt innan du tar en ögonblicksbild för att rensa alla processer som pågår.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen 

Så här skapar du en ögonblicksbild: 
1.  På [Azure-portalen](https://portal.azure.com)väljer du **Skapa en resurs**.
2. Sök efter och välj **Ögonblicksbild**.
3. Välj **Skapa**i fönstret **Ögonblicksbild.** Fönstret **Skapa ögonblicksbild** visas.
4. Ange ett **namn** för ögonblicksbilden.
5. Markera en befintlig [resursgrupp](../../azure-resource-manager/management/overview.md#resource-groups) eller ange namnet på en ny. 
6. Välj en **Plats** för ett Azure-datacenter.  
7. För **källdisk**väljer du den hanterade disken som ska ögonblicksbild.
8. Välj den **kontotyp** som ska användas för att lagra ögonblicksbilden. Välj **Standard_HDD**, om du inte behöver ögonblicksbilden som ska lagras på en högpresterande disk.
9. Välj **Skapa**.

## <a name="use-powershell"></a>Använd PowerShell

Följande steg visar hur du kopierar VHD-disken och skapar ögonblicksbildkonfigurationen. Du kan sedan ta en ögonblicksbild av disken med hjälp av [cmdleten New-AzSnapshot.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

 

1. Ange några parametrar: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Hämta den virtuella datorn:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Skapa ögonblicksbildkonfigurationen. I det här exemplet är ögonblicksbilden av OS-disken:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Om du vill lagra ögonblicksbilden i zontålig lagring skapar du den i en `-SkuName Standard_ZRS` region som stöder [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkluderar parametern.   
   
4. Ta ögonblicksbilden:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från en ögonblicksbild och sedan koppla den nya hanterade disken som OS-disken. Mer information finns i exemplet i [Skapa en virtuell dator från en ögonblicksbild med PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
