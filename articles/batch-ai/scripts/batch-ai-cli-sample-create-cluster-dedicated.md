---
title: Exempel på Azure CLI-skript – Skapa dedikerade Azure Batch AI-kluster | Microsoft Docs
description: Exempel på Azure CLI-skript – Skapa och hantera ett Azure Batch AI-kluster av dedikerade noder (virtuella datorer)
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 8ca2def6aa6ab463e5b2be0203791da671a41745
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697821"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-dedicated-nodes"></a>CLI-exempel: Skapa och hantera ett Azure Batch AI-kluster av dedikerade noder

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Det här skriptet innehåller några av de kommandon som används i Azure CLI för att skapa och hantera ett Azure Batch AI-kluster som består av dedikerade noder (virtuella datorer).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.38 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-dedicated.sh "Create Batch AI cluster - dedicated nodes")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommandon för att ta bort resursgrupperna och alla resurser som är kopplade till dem.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Skapar en Azure Batch AI-arbetsyta. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Skapar ett Azure Batch AI-kluster. |
| [az batchai cluster show](/cli/azure/batchai/cluster) | Visar information om ett Azure Batch AI-kluster. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node) | Visar en lista över noderna i ett Azure Batch AI-kluster. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Ändrar storlek på Azure Batch AI-klustret.  |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
