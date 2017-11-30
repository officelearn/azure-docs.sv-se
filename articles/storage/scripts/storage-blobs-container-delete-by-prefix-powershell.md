---
title: "Azure PowerShell skriptexempel - ta bort behållare efter prefix | Microsoft Docs"
description: "Ta bort Azure Storage blob-behållare baserat på namnprefixet för en behållare."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 629189b9dbe2327763d364abc95f49539a312c53
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Ta bort behållare baserat på namnprefixet för behållaren

Det här skriptet tar bort behållare i Azure Blob storage baserat på ett prefix i behållarnamn.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen återstående behållare, och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att ta bort behållare baserat på namnprefixet för behållaren. Varje objekt i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Hämtar ett angivna Storage-konto eller alla lagringskonton i en resursgrupp eller prenumerationen. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | Visar de behållare som är kopplade till ett lagringskonto. |
| [Ta bort AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Tar bort den angivna behållaren. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare lagringsutrymme PowerShell-skript-exempel finns i [PowerShell-exempel för Azure Blob storage](../blobs/storage-samples-blobs-powershell.md).
