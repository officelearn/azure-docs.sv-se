---
title: Exempel på Azure CLI-skript – Ta bort behållare efter prefix | Microsoft Docs
description: Ta bort Azure Storage-blobbehållare baserat på behållarens namnprefix.
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
ms.openlocfilehash: 9e93dc14a4729011f74c5eafe94528608b89116f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29848295"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Ta bort behållare baserat på behållarens namnprefix

Det här skriptet skapar först några exempelbehållare i Azure Blob-lagring och tar sedan bort några av behållarna baserat på ett prefix i behållarnamnet.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, återstående behållare och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att ta bort behållare baserat på behållarens namnprefix. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Skapar ett Azure Storage-konto i den angivna resursgruppen. |
| [az storage container create](/cli/azure/storage/container#az_storage_container_create) | Skapar en behållare i Azure Blob-lagring. |
| [az storage container list](/cli/azure/storage/container#az_storage_container_list) | Visar en lista över behållarna i ett Azure Storage-konto. |
| [az storage container delete](/cli/azure/storage/container#az_storage_container_delete) | Tar bort behållare i ett Azure Storage-konto. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för lagring finns i [Azure CLI-exempel för Azure Storage](../blobs/storage-samples-blobs-cli.md).
