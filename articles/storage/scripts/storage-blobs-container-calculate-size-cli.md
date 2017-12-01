---
title: "Azure CLI-skript Sample - beräkna blob-behållaren storlek | Microsoft Docs"
description: "Beräkna storleken på en behållare i Azure Blob storage addera storleken på blobbar i behållaren."
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
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: 61a553e47a642aead323a19d0724fdccc94a6282
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Beräkna storleken på en behållare för Blob storage

Det här skriptet beräknar storleken på en behållare i Azure Blob storage addera storleken på blobbar i behållaren.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Det här skriptet CLI ger en uppskattad storlek för behållaren och ska inte användas för fakturering beräkningar.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, behållaren och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att beräkna storleken på Blob storage-behållare. Varje objekt i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [ladda upp AZ storage-blob](/cli/azure/storage/account#create) | Överför lokala filer till en Azure Blob storage-behållare. |
| [AZ storage blob-lista](/cli/azure/storage/account/keys#list) | Visar en lista över blobbar i en Azure Blob storage-behållare. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](/cli/azure/overview).

Ytterligare lagringsutrymme CLI skriptexempel finns i den [Azure CLI prover för Azure Blob storage](../blobs/storage-samples-blobs-cli.md).
