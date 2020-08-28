---
title: Etablera dataflöde för en container i Azure Cosmos DB
description: 'Lär dig hur du etablerar data flöde på behållar nivån i Azure Cosmos DB att använda Azure Portal, CLI, PowerShell och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mjbrown
ms.custom: devx-track-javascript, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: cf2bd616fc739d233826769baaa14298d0fcec2d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017132"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container"></a>Etablera standard (manuell) genom strömning på en Azure Cosmos-behållare

Den här artikeln beskriver hur du etablerar standard-genomflödet (manuellt) i en behållare (samling, Graf eller tabell) i Azure Cosmos DB. Du kan etablera data flöde på en enskild behållare eller [etablera data flöde på en databas](how-to-provision-database-throughput.md) och dela den mellan behållarna i databasen. Du kan etablera data flöde på en behållare med hjälp av Azure Portal, Azure CLI eller Azure Cosmos DB SDK: er.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Öppna fönsterrutan **Data Explorer** och välj **Ny samling**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett ID för behållare (eller tabell eller graf).
   * Ange ett partitionsnyckelvärde (till exempel `/userid`).
   * Ange ett data flöde som du vill etablera (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png" alt-text="Skärmbild av Data Explorer med Ny samling markerat":::

## <a name="azure-cli-or-powershell"></a>Azure CLI eller PowerShell

Om du vill skapa en behållare med dedikerat data flöde ser du

* [Skapa en container med hjälp av Azure CLI](manage-with-cli.md#create-a-container)
* [Skapa en behållare med hjälp av PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> Om du konfigurerar data flödet på en behållare i ett Azure Cosmos-konto som kon figurer ATS med Azure Cosmos DB-API för MongoDB, använder du `/myShardKey` för sökvägen till partitionsnyckel. Om du konfigurerar data flödet på en behållare i ett Azure Cosmos-konto som kon figurer ATS med API för Cassandra använder du `/myPrimaryKey` för sökvägen till partitionsnyckel.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Använd Cosmos SDK: er för SQL API för att etablera data flöde för alla Cosmos DB-API: er, förutom Cassandra och MongoDB-API.

### <a name="sql-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL-, Gremlin-och tabell-API: er

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

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

### <a name="mongodb-api"></a><a id="dotnet-mongodb"></a>MongoDB-API

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra-API

Liknande kommandon kan utfärdas via alla CQL-kompatibla driv rutiner.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Ändra eller ändra data flöde för Cassandra-tabellen

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Så här etablerar du standard data flöde (manuell) i en databas](how-to-provision-database-throughput.md)
* [Så här etablerar du data flöde för autoskalning på en databas](how-to-provision-autoscale-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
