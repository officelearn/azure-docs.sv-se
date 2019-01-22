---
title: Skapa en ögonblicksbild av en virtuell Hårddisk i Azure | Microsoft Docs
description: Lär dig hur du skapar en kopia av en virtuell Azure-dator ska användas som en tillbaka upp eller för att felsöka problem.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: a853df2aba0fb8b1ca2449c70794d974151c2546
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428842"
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

Följande steg visar hur du kopierar VHD-disken, skapa ögonblicksbild konfigurationen och ta en ögonblicksbild av disken med hjälp av den [New AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet. 

Innan du börjar måste du kontrollera att du har den senaste versionen av AzureRM.Compute PowerShell-modulen, som måste vara version 5.7.0-installationsprogram eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt, kör [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) att skapa en anslutning till Azure.

1. Ange vissa parametrar: 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

2. Hämta den virtuella datorn:

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
```

3. Skapa ögonblicksbild-konfigurationen. I det här exemplet är ögonblicksbilden av OS-disken:

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
```
   
   > [!NOTE]
   > Om du vill lagra dina ögonblicksbild i zonen flexibel lagring, skapar du den i en region som har stöd för [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkludera den `-SkuName Standard_ZRS` parametern.   
   
4. Ta ögonblicksbilden:

 ```azurepowershell-interactive
New-AzureRmSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
```


## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från en ögonblicksbild och sedan koppla ny hanterad disk som OS-disk. Mer information finns i exemplet i [skapa en virtuell dator från en ögonblicksbild med PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
