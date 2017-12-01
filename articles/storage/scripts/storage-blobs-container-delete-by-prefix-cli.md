---
title: "Skriptexempel Azure CLI - ta bort behållare efter prefix | Microsoft Docs"
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
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: d14195abf1c17d11e259ed9edb5112626b063112
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Ta bort behållare baserat på namnprefixet för behållaren

Det här skriptet först skapar några exempel behållare i Azure Blob storage och sedan tar bort några av de behållare som baseras på ett prefix i behållarnamn.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen återstående behållare, och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att ta bort behållare baserat på namnprefixet för behållaren. Varje objekt i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ storage-konto](/cli/azure/storage/account#create) | Skapar ett Azure Storage-konto i den angivna resursgruppen. |
| [Skapa AZ lagringsbehållare](/cli/azure/storage/container#create) | Skapar en behållare i Azure Blob storage. |
| [AZ lagring behållaren lista](/cli/azure/storage/container#list) | Visar behållarna i ett Azure Storage-konto. |
| [ta bort AZ storage-behållare](/cli/azure/storage/container#delete) | Tar bort behållare i ett Azure Storage-konto. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](/cli/azure/overview).

Ytterligare lagringsutrymme CLI skriptexempel finns i den [Azure CLI-exempel för Azure Storage](../blobs/storage-samples-blobs-cli.md).
