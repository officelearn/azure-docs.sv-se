---
title: Etablera dataflöde för en container i Azure Cosmos DB
description: Lär dig att etablera dataflöde på containernivå i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: d092844fbd75fc2307cc13ec9b7779ae9237535d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61054652"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Etablera dataflöde i en Azure Cosmos-container

Den här artikeln beskriver hur du etablera dataflöde för en behållare (samling, graph eller tabell) i Azure Cosmos DB. Du kan etablera dataflöden för en enskild behållare eller [etablera dataflöde för en databas](how-to-provision-database-throughput.md) och dela den med behållare i databasen. Du kan etablera dataflöden för en behållare med Azure portal, Azure CLI eller Azure Cosmos DB SDK: er.

## <a name="provision-throughput-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account), eller välj ett befintligt Azure Cosmos-konto.

1. Öppna fönsterrutan **Data Explorer** och välj **Ny samling**. Ange därefter följande information:

   * Ange huruvida du skapar en ny databas eller använder en befintlig.
   * Ange en behållare (eller tabell eller diagram)-ID.
   * Ange ett partitionsnyckelvärde (till exempel `/userid`).
   * Ange ett dataflöde som du vill att etablera (till exempel 1000 ru: er).
   * Välj **OK**.

![Skärmbild av Data Explorer med Ny samling markerat](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Etablera dataflöde med hjälp av Azure CLI

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

Om du etablerar dataflödet i en behållare i ett Azure Cosmos-konto som har konfigurerats med Azure Cosmos DB API för MongoDB använder `/myShardKey` för partitionen som Nyckelsökväg. Om du etablerar dataflödet i en behållare i ett Azure Cosmos-konto som har konfigurerats med Cassandra API använder `/myPrimaryKey` för partitionen som Nyckelsökväg.

## <a name="provision-throughput-by-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Använd SDK: er för Cosmos för SQL-API att etablera dataflöde för alla Cosmos DB API: er, utom Cassandra API.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin och tabell-API:er

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>API för Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Hur du etablera dataflöde för en databas](how-to-provision-database-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
