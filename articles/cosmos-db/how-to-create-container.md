---
title: Skapa en container i Azure Cosmos DB
description: Lär dig att skapa en container i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 445d9f220a215eb17436d52f637b57bd3492aaae
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864782"
---
# <a name="create-an-azure-cosmos-container"></a>Skapa en Azure Cosmos-container

I den här artikeln beskrivs olika sätt att skapa en container (samling, tabell eller graf). Du kan använda Azure-portalen, Azure CLI eller SDK:er som stöds. Den här artikeln visar hur du skapar en container, anger partitionsnyckeln och etablerar dataflöde.

## <a name="create-a-container-by-using-azure-portal"></a>Skapa en container med hjälp av Azure-portalen

### <a id="portal-sql"></a>SQL-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-sql-api-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna fönsterrutan **Datautforskaren** och välj **Ny samling**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett samlings-ID.
   * Ange en partitionsnyckel.
   * Ange ett dataflöde (till exempel 1000 RU:er).
   * Välj **OK**.

![Skärmbild av Data Explorer-fönstret med Ny samling markerat](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API för Azure Cosmos DB för MongoDB

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-mongodb-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna fönsterrutan **Datautforskaren** och välj **Ny samling**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett samlings-ID.
   * Välj **obegränsad** lagringskapacitet.
   * Ange en shardnyckel.
   * Ange ett dataflöde (till exempel 1000 RU:er).
   * Välj **OK**.

![Skärmbild av API för Azure Cosmos DB för MongoDB, dialogrutan Lägg till samling](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API för Cassandra

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-cassandra-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Ange därefter följande information:

   * Ange om du skapar ett nytt nyckelutrymme eller använder ett befintligt.
   * Ange ett tabellnamn.
   * Ange egenskaper och ange en primärnyckel.
   * Ange ett dataflöde (till exempel 1000 RU:er).
   * Välj **OK**.

![Skärmbild av API för Cassandra, dialogrutan Lägg till tabell](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> För API för Cassandra används den primära nyckeln som partitionsnyckel.

### <a id="portal-gremlin"></a>Gremlin-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-graph-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny graf**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett graf-ID.
   * Välj **obegränsad** lagringskapacitet.
   * Ange en partitionsnyckel för hörn.
   * Ange ett dataflöde (till exempel 1000 RU:er).
   * Välj **OK**.

![Skärmbild av API för Gremlin, dialogrutan Lägg till diagram](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Tabell-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-table-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Ange därefter följande information:

   * Ange ett tabell-ID.
   * Välj **obegränsad** lagringskapacitet.
   * Ange ett dataflöde (till exempel 1000 RU:er).
   * Välj **OK**.

![Skärmbild av Tabell-API, dialogrutan Lägg till tabell](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> För tabell-API anges partitionsnyckeln varje gång du lägger till en ny rad.

## <a name="create-a-container-by-using-azure-cli"></a>Skapa en container med hjälp av Azure CLI

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

## <a name="create-a-container-by-using-net-sdk"></a>Skapa en container med hjälp av .NET SDK

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
MongoDB-kabelprotokollet har inga begäransenheter. Skapa en ny samling med dataflöde genom att använda Azure-portalen eller SQL-API.

### <a id="dotnet-cassandra"></a>API för Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Nästa steg

- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
