---
title: Exempel på Azure CLI-skript – Ta bort containrar efter prefix | Microsoft Docs
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
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 01187a4dbcd8333f95cf20b5956b7b81559a19a8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730660"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Ta bort containrar baserat på containerns namnprefix

Det här skriptet skapar först några exempelcontainrar i Azure Blob-lagring och tar sedan bort några av containrarna baserat på ett prefix i containernamnet.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, återstående containrar och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att ta bort containrar baserat på containerns namnprefix. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account) | Skapar ett Azure Storage-konto i den angivna resursgruppen. |
| [az storage container create](/cli/azure/storage/container) | Skapar en container i Azure Blob-lagring. |
| [az storage container list](/cli/azure/storage/container) | Visar en lista över containrarna i ett Azure Storage-konto. |
| [az storage container delete](/cli/azure/storage/container) | Tar bort containrar i ett Azure Storage-konto. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för lagring finns i [Azure CLI-exempel för Azure Storage](../blobs/storage-samples-blobs-cli.md).
