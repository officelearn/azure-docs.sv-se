---
title: Exempel på Azure CLI-skript – Beräkna blob-containerns storlek | Microsoft Docs
description: Beräkna storleken på en container i Azure Blob-lagring genom att addera storleken på blobarna i containern.
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
ms.openlocfilehash: 3cb1e35617a58fcde7968ab45d437d865c91f983
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245079"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Beräkna storleken på en container för Blob-lagring

Det här skriptet beräknar storleken på en container i Azure Blob-lagring genom att addera storleken på blobarna i containern.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Det här CLI-skriptet ger en uppskattad storlek för containern och ska inte användas för faktureringsberäkningar.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, containern och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att beräkna storleken på Blob-lagringscontainern. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage blob upload](/cli/azure/storage/account) | Överför lokala filer till en Azure Blob Storage-container. |
| [az storage blob list](/cli/azure/storage/account/keys) | Visar en lista över blobarna i en Azure Blob Storage-container. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för lagring finns i [Azure CLI-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-cli.md).
