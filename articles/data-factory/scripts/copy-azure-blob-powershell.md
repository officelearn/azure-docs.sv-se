---
title: 'PowerShell-skript: Kopiera data i molnet med Azure Data Factory | Microsoft Docs'
description: Den här PowerShell.skript kopierar data från en plats i Azure Blob Storage till en annan plats i samma Blob Storage.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: jingwang
ms.openlocfilehash: 19cb37ea40daa1e416f0dea82b2ddc8ad107454c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436575"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Använd PowerShell för att skapa data factory-pipeline för att kopiera data i molnet

Det här Skriptexemplet för PowerShell skapar en pipeline i Azure Data Factory som kopierar data från en plats till en annan plats i Azure Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Azure Storage-konto**. Du kan använda blob-lagringen både som **källa** och **mottagare** för datalagringen. Om du inte har något Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md). 
* Skapa en **blobcontainer** i Blob Storage, skapa en **indatamapp** i containern och ladda upp några filer till mappen. Du kan använda verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ansluta till Azure Blob Storage, skapa en blobcontainer, ladda upp en indatafil och verifiera utdatafilen.

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory-entiteter (länkade tjänsten, datauppsättningen och pipeline) på din hårddisk i mappen c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>Rensa distribution

När du kör exempelskriptet kan använda du följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Om du vill ta bort datafabriken från resursgruppen, kör du följande kommando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Skapa en datafabrik. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Skapar en länkad tjänst i datafabriken. En länkad tjänst länkar ett datalager eller beräkningar till en data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Skapar en datauppsättning i data factory. En datauppsättning som representerar indata/utdata för en aktivitet i en pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Skapar en pipeline i datafabriken. En pipeline som innehåller en eller flera aktiviteter som utför en viss åtgärd. I denna pipeline kopierar en Kopieringsaktivitet data från en plats till en annan plats i Azure Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Skapar en körning för pipelinen. Med andra ord kör pipelinen. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Hämtar information om körning av aktiviteten (aktivitetskörning) i pipelinen. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Factory PowerShell-skriptexempel finns i den [Azure Data Factory PowerShell-exempel](../samples-powershell.md).
