---
title: "Skapa en ögonblicksbild av en virtuell Hårddisk i Azure | Microsoft Docs"
description: "Lär dig hur du skapar en kopia av en Azure VM ska användas som en tillbaka upp eller för att felsöka problem."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: dba70db512d88dfc57107bade0df50d1834eb883
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Ta en ögonblicksbild av ett operativsystem eller disk VHD för säkerhetskopiering eller felsökning av VM-problem. En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell Hårddisk. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Använd Azure-portalen för att ta en ögonblicksbild 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Starta i det övre vänstra hörnet, klickar du på **ny** och Sök efter **ögonblicksbild**.
3. I bladet ögonblicksbild klickar du på **skapa**.
4. Ange en **namn** för ögonblicksbilden.
5. Välj en befintlig [resursgrupp](../../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny. 
6. Välj ett Azure-datacenter plats.  
7. För **källdisken**, Välj den hanterade disken till ögonblicksbild.
8. Välj den **kontotyp** du vill lagra ögonblicksbilden. Vi rekommenderar **Standard_LRS** om du inte behöver den lagras på en disk med hög prestanda.
9. Klicka på **Skapa**.

## <a name="use-powershell-to-take-a-snapshot"></a>Använd PowerShell för att ta en ögonblicksbild
Följande steg visar hur du hämtar VHD-disk som ska kopieras, skapa ögonblicksbild konfigurationer och ta en ögonblicksbild av disken med hjälp av den [ny AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet. 

Kontrollera att du har den senaste versionen av den AzureRM.Compute PowerShell module installerad. Kör följande kommando för att installera den.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Mer information finns i [Azure PowerShell versionshantering](/powershell/azure/overview).


1. Ange vissa parametrar. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Hämta VHD-disk som ska kopieras.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Skapa ögonblicksbild konfigurationer. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Ta ögonblicksbilden.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Om du planerar att använda ögonblicksbilden för att skapa en hanterad Disk och koppla den till en virtuell dator som måste vara höga prestanda, använder du parametern `-AccountType Premium_LRS` med kommandot Ny AzureRmSnapshot. Parametern skapar ögonblicksbilden så att den lagras som en Premium hanteras Disk. Premium-hanterade diskar är dyrare än Standard. Vara säker på att du verkligen behöver Premium innan du använder parametern.

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från en ögonblicksbild och kopplar den nya hantera disken som OS-disk. Mer information finns i [skapa en virtuell dator från en ögonblicksbild](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) exempel.
