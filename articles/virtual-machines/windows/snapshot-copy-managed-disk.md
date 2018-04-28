---
title: Skapa en ögonblicksbild av en virtuell Hårddisk i Azure | Microsoft Docs
description: Lär dig hur du skapar en kopia av en Azure VM ska användas som en tillbaka upp eller för att felsöka problem.
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: d7315d3fb7fc156beb85271d0e5aa19ec6baa7a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Ta en ögonblicksbild av ett operativsystem eller disk VHD för säkerhetskopiering eller felsökning av VM-problem. En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell Hårddisk. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Använd Azure-portalen för att ta en ögonblicksbild 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Starta i det övre vänstra hörnet, klickar du på **skapar du en resurs** och Sök efter **ögonblicksbild**.
3. I bladet ögonblicksbild klickar du på **skapa**.
4. Ange en **namn** för ögonblicksbilden.
5. Välj en befintlig [resursgrupp](../../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny. 
6. Välj ett Azure-datacenter plats.  
7. För **källdisken**, Välj den hanterade disken till ögonblicksbild.
8. Välj den **kontotyp** du vill lagra ögonblicksbilden. Vi rekommenderar **Standard_LRS** om du inte behöver den lagras på en disk med hög prestanda.
9. Klicka på **Skapa**.

## <a name="use-powershell-to-take-a-snapshot"></a>Använd PowerShell för att ta en ögonblicksbild

Följande steg visar hur du hämtar VHD-disk som ska kopieras, skapa ögonblicksbild konfigurationer och ta en ögonblicksbild av disken med hjälp av den [ny AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet. 

Innan du börjar bör du kontrollera att du har den senaste versionen av AzureRM.Compute PowerShell-modulen. Den här artikeln kräver AzureRM Modulversion 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

Ange vissa parametrar. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

Hämta den virtuella datorn.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

Skapa en ögonblicksbild av konfigurationen. I det här exemplet ska vi ögonblicksbild OS-disk.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Om du vill lagra din ögonblicksbild i zonen flexibel måste du skapa i en region som stöder [tillgänglighet zoner](../../availability-zones/az-overview.md) och inkludera den `-SkuName Standard_ZRS` parameter.   

   
Ta ögonblicksbilden.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från en ögonblicksbild och kopplar den nya hantera disken som OS-disk. Mer information finns i [skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) exempel.
