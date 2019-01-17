---
title: Exempel på Azure PowerShell-skript – Beräkna storlek på blob-container | Microsoft Docs
description: Beräkna storleken på en container i Azure Blob-lagring genom att addera storleken på dess blobar.
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
ms.openlocfilehash: 03a0f8885d51e6d45fabda86714afdeefc01f992
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213492"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Beräkna storleken på en container för Blob-lagring

Det här skriptet beräknar storleken på en container i Azure Blob-lagring genom att addera storleken på blobarna i containern.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Det här PowerShell-skriptet ger en uppskattad storlek för containern och ska inte användas för faktureringsberäkningar. Information om ett skript som beräknar containerstorlek för faktureringsändamål finns i [Beräkna storleken på en Blob Storage-container för faktureringsändamål](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, containern och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att beräkna storleken på Blob-lagringscontainern. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Hämtar ett angivet lagringskonto eller alla lagringskonton i en resursgrupp eller i prenumerationen. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Visar en lista över blobar i en container. ||

## <a name="next-steps"></a>Nästa steg

Information om ett skript som beräknar containerstorlek för faktureringsändamål finns i [Beräkna storleken på en Blob Storage-container för faktureringsändamål](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för lagring finns i [PowerShell-exempel för Azure Storage](../blobs/storage-samples-blobs-powershell.md).
