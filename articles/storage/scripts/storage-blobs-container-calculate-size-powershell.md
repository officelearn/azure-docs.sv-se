---
title: Azure PowerShell-skript Sample - beräkna blob-behållaren storlek | Microsoft Docs
description: Beräkna storleken på en behållare i Azure Blob storage addera storleken på var och en av dess blobbar.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: f6f421e780bfbb7922a4b11f758330f2a9a0b84b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
ms.locfileid: "24814583"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Beräkna storleken på en behållare för Blob storage

Det här skriptet beräknar storleken på en behållare i Azure Blob storage addera storleken på blobbar i behållaren.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Det här PowerShell-skriptet ger en uppskattad storlek för behållaren och ska inte användas för fakturering beräkningar. Ett skript som beräknar behållarens storlek för fakturering, se [beräkna storleken på en behållare för Blob storage för fakturering](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, behållaren och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att beräkna storleken på Blob storage-behållare. Varje objekt i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Hämtar ett angivna Storage-konto eller alla lagringskonton i en resursgrupp eller prenumerationen. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | Visar en lista över blobbar i en behållare. ||

## <a name="next-steps"></a>Nästa steg

Ett skript som beräknar behållarens storlek för fakturering, se [beräkna storleken på en behållare för Blob storage för fakturering](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare lagringsutrymme PowerShell-skript-exempel finns i [PowerShell-exempel för Azure Storage](../blobs/storage-samples-blobs-powershell.md).
