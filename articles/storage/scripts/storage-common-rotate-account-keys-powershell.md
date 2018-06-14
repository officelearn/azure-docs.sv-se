---
title: Azure PowerShell skriptexempel - åtkomstnyckeln för lagringskontot rotera | Microsoft Docs
description: Skapa ett Azure Storage-konto och sedan hämta och rotera en av dess åtkomstnycklarna för kontot.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: a7aaacf316799540a6a72b699ba8ea8bb389c8a8
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
ms.locfileid: "25984176"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Skapa ett lagringskonto och rotera åtkomstnycklar sitt konto

Det här skriptet skapar ett Azure Storage-konto, visas det nya lagringskontot primärnyckeln och sedan förnyar (roterar) nyckeln.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, storage-konto och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa storage-konto och hämta och rotera en av dess åtkomstnycklar. Varje objekt i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) | Hämtar alla platser och vilka resursproviders för varje plats. |
| [Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar ett Azure-resursgrupp. |
| [Ny AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Skapar ett lagringskonto. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Hämtar åtkomstnycklarna för ett Azure Storage-konto. |
| [Ny AzureRmStorageAccountKey](/powershell/module/azurerm.storage/new-azurermstorageaccountkey) | Återskapar snabbtangent för ett Azure Storage-konto. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare lagringsutrymme PowerShell-skript-exempel finns i [PowerShell-exempel för Azure Blob storage](../blobs/storage-samples-blobs-powershell.md).
