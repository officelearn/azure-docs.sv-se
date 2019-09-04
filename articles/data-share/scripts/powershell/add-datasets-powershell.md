---
title: 'PowerShell-skript: Lägga till en BLOB-datauppsättning till en Azure-Dataresurs | Microsoft Docs'
description: Det här PowerShell-skriptet lägger till en BLOB-datauppsättning till en befintlig resurs.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: ef027387d6c8e119c03fef6e4e5d7fd7a4125e51
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243040"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Använd PowerShell för att skapa en data resurs i Azure

Det här PowerShell-skriptet lägger till en BLOB-datauppsättning till en befintlig data resurs.

## <a name="sample-script"></a>Exempelskript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzDataShareDataSet](/powershell/module/az.resources/new-azdatasharedataset) | Lägger till en data uppsättning till en data resurs. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
