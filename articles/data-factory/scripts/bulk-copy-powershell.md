---
title: "PowerShell-skript: kopiera data i grupp med hjälp av Azure Data Factory | Microsoft Docs"
description: "Detta PowerShell-skript visar hur du använder Azure Data Factory för att kopiera data från ett dataarkiv som källa till ett dataarkiv som mål i grupp."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: f8a66f3daab9b57723f67a60fa195e53333f3b8d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell script - kopiera flera tabeller i grupp med hjälp av Azure Data Factory

Det här exempelskriptet PowerShell kopierar data från flera tabeller i en Azure SQL database till ett Azure SQL data warehouse.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Se [Självstudier: masskopiera](../tutorial-bulk-copy.md#prerequisites) förutsättningarna för att köra det här exemplet.

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory-enheter (länkade tjänsten dataset och pipeline) på din hårddisk i mappen c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Rensa distribution

Du kan använda följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den när du har kört exempelskript:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Ta bort datafabriken från resursgruppen genom att köra följande kommando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Skapa en datafabrik. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Skapar en länkad tjänst i datafabriken. En länkad tjänst länkar en datalagret eller beräkning till en data factory. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Skapar en datamängd i datafabriken. En datauppsättning representerar o för en aktivitet i en pipeline. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Skapar en pipeline i datafabriken. En pipeline innehåller en eller flera aktiviteter som utför en viss åtgärd. I den här pipelinen kopieras en kopieringsaktiviteten data från en plats till en annan plats i ett Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Skapar en körning för pipeline. Med andra ord körs pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Hämtar information om körning av aktiviteten (aktivitet kör) i pipelinen. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Factory PowerShell skriptexempel finns i den [Azure Data Factory PowerShell-skript](../samples-powershell.md).