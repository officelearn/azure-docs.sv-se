---
title: Hantera Azure Cosmos DB-resurser med Azure CLI
description: Använd Azure CLI för att hantera ditt Azure Cosmos DB konto, databas och behållare.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f9d8bf9161343e4b36a3c16209873962b69d8af5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615221"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Hantera Azure Cosmos-resurser med Azure CLI

I följande guide beskrivs vanliga kommandon för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och behållare med hjälp av Azure CLI. Det finns referenssidor för alla Azure Cosmos DB CLI-kommandon i [referensguiden för Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Du kan också hitta fler exempel i [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md), inklusive hur du skapar och hanterar Cosmos DB-konton, databaser och behållare för MongoDB, Gremlin, Cassandra och tabell-API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Om du vill skapa ett Azure Cosmos DB konto med SQL API, konsekvens i sessioner i regionerna östra USA och västra USA, kör du följande kommando:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Namnet på Azure Cosmos-kontot måste vara gemener.

## <a name="create-a-database"></a>Skapa en databas

Kör följande kommando för att skapa en Cosmos-databas:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Skapa en container

Om du vill skapa en Cosmos-behållare med RU/s av 400 och en partitionsnyckel kör du följande kommando:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>Ändra data flödet för en behållare

Om du vill ändra data flödet för en Cosmos-behållare till 1000 RU/s kör du följande kommando:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Lista konto nycklar

Kör följande kommando för att hämta nycklar för ditt Cosmos-konto:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Lista anslutnings strängar

Kör följande kommando för att hämta anslutnings strängarna för ditt Cosmos-konto:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Återskapa konto nyckel

Om du vill återskapa en ny primär nyckel för ditt Cosmos-konto kör du följande kommando:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i:

- [Installera Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI-referens](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Ytterligare Azure CLI-exempel för Azure Cosmos DB](cli-samples.md)
