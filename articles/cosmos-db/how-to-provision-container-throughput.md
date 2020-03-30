---
title: Etablera dataflöde för en container i Azure Cosmos DB
description: Lär dig hur du etablerar dataflöde på behållarnivå i Azure Cosmos DB med Hjälp av Azure Portal, CLI, PowerShell och flera andra SDK:er.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273295"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Etablera dataflöde i en Azure Cosmos-container

I den här artikeln beskrivs hur du etablerar dataflöde på en behållare (samling, diagram eller tabell) i Azure Cosmos DB. Du kan etablera dataflöde på en enda behållare eller [etablera dataflöde i en databas](how-to-provision-database-throughput.md) och dela det mellan behållarna i databasen. Du kan etablera dataflöde på en behållare med Azure Portal, Azure CLI eller Azure Cosmos DB SDK:er.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller välj ett befintligt Azure Cosmos-konto.

1. Öppna fönsterrutan **Data Explorer** och välj **Ny samling**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett container-ID (eller Tabell eller Diagram).
   * Ange ett partitionsnyckelvärde (till exempel `/userid`).
   * Ange ett dataflöde som du vill etablera (till exempel 1000 ru: er).
   * Välj **OK**.

    ![Skärmbild av Data Explorer med Ny samling markerat](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI eller PowerShell

Om du vill skapa en behållare med dedikerat dataflöde

* [Skapa en container med hjälp av Azure CLI](manage-with-cli.md#create-a-container)
* [Skapa en behållare med Powershell](manage-with-powershell.md#create-container)

> [!Note]
> Om du etablerar dataflöde på en behållare i ett Azure Cosmos-konto som konfigurerats `/myShardKey` med Azure Cosmos DB API för MongoDB använder du för sökvägen till partitionsnyckeln. Om du etablerar dataflöde på en behållare i ett Azure Cosmos-konto som konfigurerats med Cassandra API använder du `/myPrimaryKey` för sökvägen till partitionsnyckeln.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Använd Cosmos SDK:er för SQL API för att etablera dataflöde för alla Cosmos DB-API:er, utom Cassandra API.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL, MongoDB, Gremlin och tabell-API:er
### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API för Cassandra

Liknande kommandon kan utfärdas via alla CQL-kompatibla drivrutiner.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Ändra eller ändra dataflöde för cassandra-tabell

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Etablera dataflöde i en databas](how-to-provision-database-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
