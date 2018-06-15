---
title: Exempel på Azure CLI-skript – Beräkna blob-behållarens storlek | Microsoft Docs
description: Beräkna storleken på en behållare i Azure Blob-lagring genom att addera storleken på blobarna i behållaren.
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
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: c38a49e82a71a23fdf621f5ac350c4242ffc2f8f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847115"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Beräkna storleken på en behållare för Blob-lagring

Det här skriptet beräknar storleken på en behållare i Azure Blob-lagring genom att addera storleken på blobarna i behållaren.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Det här CLI-skriptet ger en uppskattad storlek för behållaren och ska inte användas för faktureringsberäkningar.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, behållaren och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att beräkna storleken på Blob-lagringsbehållaren. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage blob upload](/cli/azure/storage/account#az_storage_account_create) | Överför lokala filer till en Azure Blob Storage-behållare. |
| [az storage blob list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | Visar en lista över blobarna i en Azure Blob Storage-behållare. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för lagring finns i [Azure CLI-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-cli.md).
