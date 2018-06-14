---
title: 'PowerShell-skript: kopiera data i molnet med hjälp av Azure Data Factory | Microsoft Docs'
description: Detta PowerShell-skript kopierar data från en plats i ett Azure Blob Storage till en annan plats i samma Blob Storage.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: jingwang
ms.openlocfilehash: 0b7552bb981a5b5a4850778709c08323b0e9124f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30167559"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Använd PowerShell för att skapa data factory-pipelinen för att kopiera data i molnet

PowerShell-exempelskriptet skapar en pipeline i Azure Data Factory som kopierar data från en plats till en annan plats i ett Azure Blob Storage.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Azure Storage-konto**. Du kan använda blob-lagringen både som **källa** och **mottagare** för datalagringen. Om du inte har något Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Skapa en **blob-behållare** i Blob Storage, skapa en **indatamapp** i behållaren och ladda upp några filer till mappen. Du kan använda verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ansluta till Azure Blob Storage, skapa en blob-behållare, ladda upp en indatafil och verifiera utdatafilen.

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory-enheter (länkade tjänsten dataset och pipeline) på din hårddisk i mappen c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


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

Ytterligare Azure Data Factory PowerShell skriptexempel finns i den [Azure Data Factory PowerShell-exempel](../samples-powershell.md).