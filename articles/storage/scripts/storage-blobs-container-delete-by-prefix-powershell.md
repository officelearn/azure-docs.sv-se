---
title: Exempel på Azure PowerShell-skript – Ta bort containrar efter prefix | Microsoft Docs
description: Ta bort Azure Storage-blobcontainrar baserat på containerns namnprefix.
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
ms.openlocfilehash: 18ca854f1e04127a900c1b753245ed6e2a331d68
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628176"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Ta bort containrar baserat på containerns namnprefix

Det här skriptet tar bort containrar i Azure Blob Storage baserat på ett prefix i containernamnet.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, återstående containrar och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att ta bort containrar baserat på containerns namnprefix. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Hämtar ett angivet lagringskonto eller alla lagringskonton i en resursgrupp eller i prenumerationen. |
| [Get-AzStorageContainer](/powershell/module/azure.storage/get-AzStoragecontainer) | Listar de lagringscontainrar som är associerade till ett lagringskonto. |
| [Remove-AzStorageContainer](/powershell/module/azure.storage/remove-AzStoragecontainer) | Tar bort den angivna lagringscontainern. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för lagring finns i [PowerShell-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
