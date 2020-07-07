---
title: Exempel på Azure CLI-skript – Linux-pool i batch
description: Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Linux-datornoder i Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: 21236ef2f314cb1306adc7346e620b80db540034
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964071"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-exempel: Skapa och hantera en Linux-pool i Azure Batch

Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Linux-datornoder i Azure Batch.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

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
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Visar en lista över tillgängliga SKU:er för nodagent och avbildningsinformation.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Skapar en pool med beräkningsnoder.  |
| [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) | Ändrar storlek på hur många virtuella datorer som körs i den angivna poolen.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Visar egenskaperna för en pool.  |
| [az batch node list](/cli/azure/batch/node#az-batch-node-list) | Visar en lista med alla beräkningsnoder i den angivna poolen.  |
| [az batch node reboot](/cli/azure/batch/node#az-batch-node-reboot) | Startar om den angivna beräkningsnoden.  |
| [az batch node delete](/cli/azure/batch/node#az-batch-node-delete) | Tar bort de listade noderna från den angivna poolen.  |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
