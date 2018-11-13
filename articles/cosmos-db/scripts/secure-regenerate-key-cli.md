---
title: Azure CLI-skript – Generera om Azure Cosmos DB-kontonyckel | Microsoft Docs
description: Exempel på Azure CLI-skript – Generera om en Azure Cosmos DB-kontonyckel
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 06a71ce759a72483d9ac3993e82d14af21e7d9d7
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007815"
---
# <a name="regenerate-an-azure-cosmos-db-account-key-using-the-azure-cli"></a>Generera om en Azure Cosmos DB-kontonyckel med hjälp av Azure CLI

Det här exemplet genererar om valfri typ av Azure Cosmos DB-kontonyckel med hjälp av Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-regenerate-keys/secure-cosmosdb-regenerate-keys.sh "Regenerate Azure Cosmos DB account keys")]

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Uppdaterar ett Azure Cosmos DB-konto. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys) | Listar åtkomstnycklar för ett Cosmos DB-konto. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Genererar om Azure Cosmos DB-kontonycklar. |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI-dokumentationen](../cli-samples.md).
