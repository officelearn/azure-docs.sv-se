---
title: "Exempel på Azure CLI-skript – Linux-pool i Batch | Microsoft Docs"
description: "Exempel på Azure CLI-skript – Skapa och hantera en Linux-pool i Batch"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 605db81a7f7c3d5c71992cc51d665b04aa1c589f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-exempel: Skapa och hantera en Linux-pool i Azure Batch

Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Linux-beräkningsnoder i Azure Batch.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | Visar en lista över tillgängliga SKU:er för nodagent och avbildningsinformation.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Skapar en pool med beräkningsnoder.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | Ändrar storlek på hur många virtuella datorer som körs i den angivna poolen.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Visar egenskaperna för en pool.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | Visar en lista med alla beräkningsnoder i den angivna poolen.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | Startar om den angivna beräkningsnoden.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | Tar bort de listade noderna från den angivna poolen.  |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
