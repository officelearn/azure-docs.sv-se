---
title: Skapa en ögonblicksbild av en virtuell Hårddisk i Azure | Microsoft Docs
description: Lär dig hur du skapar en kopia av en virtuell Azure-dator ska användas som en tillbaka upp eller för att felsöka problem.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b3b9095cd7ee3fa12523b14f59cc06820b9e4382
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692220"
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk (VHD). Du kan ta en ögonblicksbild av en OS- eller datadisken VHD som ska användas som en säkerhetskopia eller att felsöka problem med virtuella datorer (VM).

Om du tänker använda ögonblicksbilden för att skapa en ny virtuell dator rekommenderar vi att du korrekt stänga av den virtuella datorn innan du tar en ögonblicksbild, att rensa alla processer som pågår.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn, Välj **skapa en resurs**, och sedan söka efter och välja **ögonblicksbild**.
3. I den **ögonblicksbild** väljer **skapa**. Den **Skapa ögonblicksbild** fönster visas.
4. Ange en **namn** för ögonblicksbilden.
5. Välj en befintlig [resursgrupp](../../azure-resource-manager/resource-group-overview.md#resource-groups) eller ange namnet på en ny. 
6. Välj en **Plats** för ett Azure-datacenter.  
7. För **källdisken**, Välj den hantera disken till ögonblicksbild.
8. Välj den **kontotyp** du använder för att lagra ögonblicksbilden. Välj **Standard_HDD**, såvida du inte behöver ögonblicksbild lagras på en disk med hög prestanda.
9. Välj **Skapa**.

## <a name="use-powershell"></a>Använd PowerShell

Följande steg visar hur du kopierar VHD-disken, skapa ögonblicksbild konfigurationen och ta en ögonblicksbild av disken med hjälp av den [New AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

1. Ange vissa parametrar: 

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

3. Skapa ögonblicksbild-konfigurationen. I det här exemplet är ögonblicksbilden av OS-disken:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Om du vill lagra dina ögonblicksbild i zonen flexibel lagring, skapar du den i en region som har stöd för [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkludera den `-SkuName Standard_ZRS` parametern.   
   
4. Ta ögonblicksbilden:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från en ögonblicksbild och sedan koppla ny hanterad disk som OS-disk. Mer information finns i exemplet i [skapa en virtuell dator från en ögonblicksbild med PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
