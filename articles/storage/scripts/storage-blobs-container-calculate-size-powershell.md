---
title: Beräkna storleken på en BLOB-behållare med PowerShell
titleSuffix: Azure Storage
description: Beräkna storleken på en container i Azure Blob-lagring genom att addera storleken på dess blobar.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: de51ed7d91ba1102f5a9cd376ab95f49dd54d9f3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067075"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Beräkna storleken på en BLOB-behållare med PowerShell

Det här skriptet beräknar storleken på en container i Azure Blob-lagring genom att addera storleken på blobarna i containern.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

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

| Kommando | Obs! |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Hämtar ett angivet lagringskonto eller alla lagringskonton i en resursgrupp eller i prenumerationen. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Visar en lista över blobar i en container. |

## <a name="next-steps"></a>Nästa steg

Information om ett skript som beräknar containerstorlek för faktureringsändamål finns i [Beräkna storleken på en Blob Storage-container för faktureringsändamål](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för lagring finns i [PowerShell-exempel för Azure Storage](../blobs/storage-samples-blobs-powershell.md).
