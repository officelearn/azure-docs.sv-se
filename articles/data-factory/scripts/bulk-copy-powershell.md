---
title: 'PowerShell-skript: masskopiera data med hjälp av Azure Data Factory | Microsoft Docs'
description: Det här PowerShell-skriptet visar hur du använder Azure Data Factory för att kopiera data från ett källdatalager till ett måldatalager gruppvis.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: dc1bf394a34c097caa68c029e11c141dbae32aab
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125147"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell-skript – kopiera flera tabeller i grupp med hjälp av Azure Data Factory

Det här PowerShell-exempelskript kopierar data från flera tabeller i en Azure SQL-databas till en Azure SQL data warehouse.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Se [självstudie: masskopiera](../tutorial-bulk-copy.md#prerequisites) för förutsättningar för att köra det här exemplet.

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory-entiteter (länkade tjänsten, datauppsättningen och pipeline) på din hårddisk i mappen c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Rensa distribution

När du kör exempelskriptet kan använda du följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Om du vill ta bort datafabriken från resursgruppen, kör du följande kommando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Skapa en datafabrik. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Skapar en länkad tjänst i datafabriken. En länkad tjänst länkar ett datalager eller beräkningar till en data factory. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Skapar en datauppsättning i data factory. En datauppsättning som representerar indata/utdata för en aktivitet i en pipeline. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2pipeline) | Skapar en pipeline i datafabriken. En pipeline som innehåller en eller flera aktiviteter som utför en viss åtgärd. I denna pipeline kopierar en Kopieringsaktivitet data från en plats till en annan plats i Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipeline) | Skapar en körning för pipelinen. Med andra ord kör pipelinen. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Hämtar information om körning av aktiviteten (aktivitetskörning) i pipelinen. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Factory PowerShell-skriptexempel finns i den [Azure Data Factory PowerShell-skript](../samples-powershell.md).
