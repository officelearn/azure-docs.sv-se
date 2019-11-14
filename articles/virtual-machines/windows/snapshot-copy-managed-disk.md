---
title: Skapa en ögonblicks bild av en virtuell hård disk i Azure
description: Lär dig hur du skapar en kopia av en virtuell Azure-dator som ska användas som säkerhets kopiering eller vid fel sökning av problem.
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
ms.openlocfilehash: b564e20ca8aa5acd7fbd4ea69ac2b1cd72e66d5e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075345"
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk (VHD). Du kan ta en ögonblicks bild av en OS-eller datadisk-VHD som ska användas som säkerhets kopia, eller för att felsöka problem med virtuella datorer (VM).

Om du vill använda ögonblicks bilden för att skapa en ny virtuell dator, rekommenderar vi att du stänger av den virtuella datorn i klartext innan du tar en ögonblicks bild, för att ta bort alla processer som pågår.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **skapa en resurs**i den vänstra menyn och Sök sedan efter och välj **ögonblicks bild**.
3. I fönstret **ögonblicks bild** väljer du **skapa**. Fönstret **skapa ögonblicks bild** visas.
4. Ange ett **namn** för ögonblicks bilden.
5. Välj en befintlig [resurs grupp](../../azure-resource-manager/resource-group-overview.md#resource-groups) eller ange namnet på en ny resurs grupp. 
6. Välj en **Plats** för ett Azure-datacenter.  
7. För **käll disk**väljer du den hanterade disk som ska avbildas.
8. Välj den **Kontotyp** som ska användas för att lagra ögonblicks bilden. Välj **Standard_HDD**, om du inte behöver att ögonblicks bilden lagras på en hög presterande disk.
9. Välj **Skapa**.

## <a name="use-powershell"></a>Använd PowerShell

Följande steg visar hur du kopierar VHD-disken, skapar ögonblicks bilds konfigurationen och tar en ögonblicks bild av disken med hjälp av cmdleten [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) . 

 

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

3. Skapa ögonblicks bilds konfigurationen. I det här exemplet är ögonblicks bilden av OS-disken:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Om du vill lagra din ögonblicks bild i en zon fri lagring skapar du den i en region som stöder [tillgänglighets zoner](../../availability-zones/az-overview.md) och inkluderar parametern `-SkuName Standard_ZRS`.   
   
4. Ta ögonblicks bilden:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en ögonblicks bild genom att skapa en hanterad disk från en ögonblicks bild och sedan ansluta den nya hanterade disken som operativ system disk. Mer information finns i exemplet i [skapa en virtuell dator från en ögonblicks bild med PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
