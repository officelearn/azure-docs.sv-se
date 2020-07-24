---
title: Rotera åtkomst nycklar för lagrings konton med PowerShell
titleSuffix: Azure Storage
description: Skapa ett Azure Storage-konto och hämta och rotera sedan en av kontoåtkomstnycklarna.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: d532782847ec09d2a480951ecd77ae7746b000c2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089386"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Rotera åtkomst nycklar för lagrings konton med PowerShell

Det här skriptet skapar ett Azure Storage-konto, visar de primära åtkomstnycklarna för det nya lagringskontot och förnyar (roterar) sedan nycklarna.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, lagringskontot och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa lagringskontot och hämta och rotera en av dess åtkomstnycklar. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Hämta alla platser och de resursprovidrar som stöds för varje plats. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Skapar ett lagringskonto. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Hämtar åtkomstnycklarna för Azure Storage-kontot. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Återskapar en åtkomstnyckel för ett Azure Storage-konto. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för lagring finns i [PowerShell-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
