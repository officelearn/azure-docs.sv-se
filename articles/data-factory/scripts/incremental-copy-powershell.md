---
title: 'PowerShell-skript: läsa in data stegvis med Azure Data Factory | Microsoft Docs'
description: Det här PowerShell-skriptet visar hur du använder Azure Data Factory för att kopiera data stegvis från en Azure SQL Database till Azure Blob Storage...
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
ms.openlocfilehash: 62f0deeccdd05f4ea9098aab42145be58bf3b328
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124906"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell-skript – stegvis läsa in data med hjälp av Azure Data Factory
Det här Skriptexemplet för PowerShell läser bara nya eller uppdaterade posterna från källans datalager till mottagarens datalager efter den inledande fullständig kopian av data från källan till mottagaren.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Se [självstudie: inkrementell kopia](../tutorial-incremental-copy-powershell.md#prerequisites) för förutsättningar för att köra det här exemplet. 

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory-entiteter (länkade tjänsten, datauppsättningen och pipeline) på din hårddisk i mappen c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1 "Incremental copy from Azure SQL Database to Azure Blob Storage")]

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
