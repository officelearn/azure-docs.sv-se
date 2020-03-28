---
title: Skapa en Gremlin-databas och ett diagram för Azure Cosmos DB
description: Skapa en Gremlin-databas och ett diagram för Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 46914952e59e5f4fc3d5cd78f80842de09b11184
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275404"
---
# <a name="create-an-azure-cosmos-gremlin-api-account-database-and-graph-using-azure-cli"></a>Skapa ett Azure Cosmos Gremlin API-konto, databas och diagram med Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver det här avsnittet att du kör Azure CLI version 2.0.73 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/create.sh "Create an Azure Cosmos DB Gremlin API account, database, and graph.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [az cosmosdb gremlin databas skapa](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Skapar en Azure Cosmos Gremlin-databas. |
| [az cosmosdb gremlin graf skapa](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Skapar ett Azure Cosmos Gremlin-diagram. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
