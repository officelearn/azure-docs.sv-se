---
title: Exempel på Azure CLI-skript – Skapa ett Batch AI-kluster med en konfigurationsfil | Microsoft Docs
description: Exempel på Azure CLI-skript – Skapa ett Batch AI-kluster genom att ange konfigurationsinställningar i en JSON-fil.
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
ms.date: 08/16/2018
ms.author: danlep
ms.openlocfilehash: 01281c0328fab85814ff93c73f9ea25e0d4c1b08
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232808"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>CLI-exempel: Skapa ett Batch AI-kluster med en konfigurationsfil

Det här skriptet visar hur du använder en JSON-konfigurationsfil för att ange inställningar för ett Batch AI-kluster. Använd de här inställningarna i stället för motsvarande kommandoradsparametrar för `az batchai cluster create`. En konfigurationsfil är användbar när du vill montera flera filsystem på klusternoderna eller vill använda identiska konfigurationer i flera kluster.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här guiden att du kör Azure CLI version 2.0.38 eller senare. Kör `az --version` för att fastställa versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommandon för att ta bort resursgrupperna och alla resurser som är kopplade till dem.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Skapar ett lagringskonto. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Skapar en filresurs i ett lagringskonto. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Skapar en Azure Batch AI-arbetsyta. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Skapar ett Azure Batch AI-kluster. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Visar information om ett Azure Batch AI-kluster. |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
