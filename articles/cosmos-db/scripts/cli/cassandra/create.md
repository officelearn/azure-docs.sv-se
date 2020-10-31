---
title: Skapa ett Cassandra-utrymme och en tabell för Azure Cosmos DB
description: Skapa ett Cassandra-utrymme och en tabell för Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 1344ce750d6a94e78cd93fe4dc859e4a05639c45
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086609"
---
# <a name="create-an-azure-cosmos-cassandra-api-account-keyspace-and-table-using-azure-cli"></a>Skapa ett Azure-Cosmos API för Cassandra konto, ett tecken utrymme och en tabell med Azure CLI
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, kräver det här avsnittet att du kör Azure CLI-version 2.9.1 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/create.sh "Create an Azure Cosmos DB Cassandra API account, keyspace, and table.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [AZ cosmosdb Cassandra-blank steg skapa](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Skapar ett Cosmos-Cassandra för Azure. |
| [AZ cosmosdb Cassandra Table Create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Skapar en Azure Cosmos Cassandra-tabell. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skript exempel finns i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
