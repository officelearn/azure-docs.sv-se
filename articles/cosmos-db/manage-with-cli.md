---
title: Hantera Azure Cosmos DB-resurser med hjälp av Azure CLI
description: Använda Azure CLI för att hantera ditt Azure Cosmos DB-konto, databas och behållare.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 4/8/2019
ms.author: mjbrown
ms.openlocfilehash: 1d19e58b2d1381725de490b68d9e4d00a2ca4cb6
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495489"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Hantera Azure Cosmos-resurser med hjälp av Azure CLI

Enligt följande anvisningar beskriver vanliga kommandon för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och behållare med hjälp av Azure CLI. Det finns referenssidor för alla Azure Cosmos DB CLI-kommandon i [referensguiden för Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Du kan också hitta fler exempel i [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md), inklusive hur du skapar och hanterar Cosmos DB-konton, databaser och behållare för MongoDB, Gremlin, Cassandra och tabell-API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Om du vill skapa ett Azure Cosmos DB-konto med SQL API, sessionskonsekvens i östra USA och västra USA-regioner, kör du följande kommando:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Azure Cosmos-kontonamnet måste vara gemener.

## <a name="create-a-database"></a>Skapa en databas

Om du vill skapa ett Cosmos DB-databasen, kör du följande kommando:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Skapa en container

Om du vill skapa en Cosmos DB-behållare med RU/s för över 400 och en partitionsnyckel, kör du följande kommando:

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

## <a name="change-the-throughput-of-a-container"></a>Ändra genomströmningen i en behållare

Om du vill ändra dataflödet för en Cosmos DB-behållare på 1000 RU/s, kör du följande kommando:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Lista nycklar

Om du vill hämta nycklar för Cosmos-konto, kör du följande kommando:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Lista anslutningssträngar

Om du vill hämta anslutningssträngarna för Cosmos-konto, kör du följande kommando:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Återskapa kontonyckeln

Om du vill återskapa en ny primär nyckel för Cosmos-konto, kör du följande kommando:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns:

- [Installera Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI-referens](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Ytterligare Azure CLI-exempel för Azure Cosmos DB](cli-samples.md)
