---
title: Skapa en container i Azure Cosmos DB
description: Lär dig att skapa en container i Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 66fdb1313177b6f6a285f7d1b1b02dadac9472cc
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53810448"
---
# <a name="create-a-container-in-azure-cosmos-db"></a>Skapa en container i Azure Cosmos DB

I den här artikeln beskrivs olika sätt att skapa en container (samling, tabell, graf). En container kan skapas med hjälp av Azure-portalen, Azure CLI eller SDK:er som stöds. Den här artikeln visar hur du skapar en container, anger partitionsnyckeln och etablerar dataflöde.

## <a name="create-a-container-using-azure-portal"></a>Skapa en container med hjälp av Azure-portalen

### <a id="portal-sql"></a>SQL-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Cosmos DB-konto](create-sql-api-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna fönsterrutan **Datautforskaren** och välj **Ny samling**. Fyll sedan i formuläret med följande information:

   * Skapa en ny databas eller välj en befintlig.
   * Ange ett samlings-ID.
   * Ange en partitionsnyckel.
   * Ange ett dataflöde, till exempel 1 000 RU.
   * Välj **OK**.

![SQL API skapar en samling](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API för Azure Cosmos DB för MongoDB

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Cosmos DB-konto](create-mongodb-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna fönsterrutan **Datautforskaren** och välj **Ny samling**. Fyll sedan i formuläret med följande information:

   * Skapa en ny databas eller välj en befintlig.
   * Ange ett samlings-ID.
   * Välj **obegränsad** lagringskapacitet.
   * Ange en shardnyckel.
   * Ange ett dataflöde, till exempel 1 000 RU.
   * Välj **OK**.

![API för Azure Cosmos DB för MongoDB skapar en mängd](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API för Cassandra

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Cosmos DB-konto](create-cassandra-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Fyll sedan i formuläret med följande information:

   * Skapa ett nytt nyckelutrymme eller välj ett befintligt.
   * Ange ett tabellnamn.
   * Ange egenskaper och ange en PRIMÄRNYCKEL.
   * Ange ett dataflöde, till exempel 1 000 RU.
   * Välj **OK**.

![API för Cassandra skapar en samling](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> För API för Cassandra används den primära nyckeln som partitionsnyckel.

### <a id="portal-gremlin"></a>Gremlin-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Cosmos DB-konto](create-graph-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny graf**. Fyll sedan i formuläret med följande information:

   * Skapa en ny databas eller välj en befintlig.
   * Ange ett graf-id.
   * Välj **obegränsad** lagringskapacitet.
   * Ange en partitionsnyckel för hörn.
   * Ange ett dataflöde, till exempel 1 000 RU.
   * Välj **OK**.

![Gremlin-API skapar en samling](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Tabell-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Cosmos DB-konto](create-table-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Fyll sedan i formuläret med följande information:

   * Ange ett tabell-id.
   * Välj **obegränsad** lagringskapacitet.
   * Ange ett dataflöde, till exempel 1 000 RU.
   * Välj **OK**.

![Tabell-API skapar en samling](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> För tabell-API anges partitionsnyckeln varje gång du lägger till en ny rad.

## <a name="create-a-container-using-azure-cli"></a>Skapa en container med hjälp av Azure CLI

### <a id="cli-sql"></a>SQL-API

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>API för Azure Cosmos DB för MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>API för Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>Gremlin-API

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>Tabell-API

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>Skapa en container med hjälp av .NET SDK

### <a id="dotnet-sql-graph"></a>SQL-API och Gremlin-API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>API för Azure Cosmos DB för MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
MongoDB-kabelprotokollet har inget koncept för begäransenheter. Skapa en ny samling med dataflöde genom att använda Azure Portal eller SQL-API så som visas i föregående exempel.

### <a id="dotnet-cassandra"></a>API för Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig om partitionering i Cosmos DB:

- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
