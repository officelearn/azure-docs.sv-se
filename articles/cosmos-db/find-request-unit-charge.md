---
title: Hitta avgiften för begäranhet (RU) i Azure Cosmos DB
description: Lär dig hur du hittar avgiften för begäranhet (RU) för alla åtgärder som utförs mot en Azure Cosmos-behållare.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: c5699bb851bd0a818a987228155c62683e93f51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585908"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Hitta enhetsavgiften för begäran i Azure Cosmos DB

Den här artikeln innehåller olika sätt att hitta [förbrukning av begärandeenheten](request-units.md) (RU) för alla åtgärder som körs mot en behållare i Azure Cosmos DB. För närvarande kan du mäta den här förbrukningen endast med hjälp av Azure-portalen eller genom att inspektera svaret som skickas tillbaka från Azure Cosmos DB via en av SDK:erna.

## <a name="sql-core-api"></a>SQL-API (Core)

Om du använder SQL API har du flera alternativ för att hitta RU-förbrukningen för en åtgärd mot en Azure Cosmos-behållare.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

För närvarande kan du hitta begäranden i Azure-portalen endast för en SQL-fråga.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account) och mata det med data, eller välj ett befintligt Azure Cosmos-konto som redan innehåller data.

1. Gå till fönstret **Data explorer** och markera sedan den behållare som du vill arbeta med.

1. Välj **Ny SQL-fråga**.

1. Ange en giltig fråga och välj sedan **Kör fråga**.

1. Välj **Frågestatistik** om du vill visa den faktiska begäranden för den begäran som du utförde.

![Skärmbild av en SQL-begärandeavgift i Azure-portalen](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Använda .NET SDK
### <a name="net-v2-sdk"></a>.Net V2 SDK

Objekt som returneras från [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) exponerar en `RequestCharge` egenskap:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

Objekt som returneras från [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) exponerar en `RequestCharge` egenskap:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Mer information finns i [Snabbstart: Skapa en .NET-webbapp med hjälp av ett SQL API-konto i Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Använda Java SDK

Objekt som returneras från [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) exponerar en `getRequestCharge()` metod:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Mer information finns i [Snabbstart: Skapa ett Java-program med hjälp av ett Azure Cosmos DB SQL API-konto](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Använda Node.js SDK

Objekt som returneras från [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) visar ett `headers` underobjekt som mappar alla rubriker som returneras av det underliggande HTTP-API:et. Begäran avgiften är tillgänglig `x-ms-request-charge` under nyckeln:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Mer information finns i [Snabbstart: Skapa en Node.js-app med hjälp av ett Azure Cosmos DB SQL API-konto](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Använda Python SDK

Objektet `CosmosClient` från [Python SDK](https://pypi.org/project/azure-cosmos/) visar `last_response_headers` en ordlista som mappar alla rubriker som returneras av det underliggande HTTP-API:et för den senaste åtgärden som kördes. Begäran avgiften är tillgänglig `x-ms-request-charge` under nyckeln:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Mer information finns i [Snabbstart: Skapa en Python-app med hjälp av ett Azure Cosmos DB SQL API-konto](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

RU-laddningen exponeras av ett `getLastRequestStatistics`anpassat [databaskommando](https://docs.mongodb.com/manual/reference/command/) med namnet . Kommandot returnerar ett dokument som innehåller namnet på den senast utförda åtgärden, dess begärandeavgift och dess varaktighet. Om du använder Azure Cosmos DB API för MongoDB har du flera alternativ för att hämta RU-avgiften.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

För närvarande kan du hitta begäran avgift i Azure-portalen endast för en fråga.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account) och mata det med data, eller välj ett befintligt konto som redan innehåller data.

1. Gå till fönstret **Data explorer** och markera sedan den behållare som du vill arbeta med.

1. Välj **ny fråga**.

1. Ange en giltig fråga och välj sedan **Kör fråga**.

1. Välj **Frågestatistik** om du vill visa den faktiska begäranden för den begäran som du utförde.

![Skärmbild av en betalning begäras av MongoDB-begäran i Azure-portalen](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Använda drivrutinen MongoDB .NET

När du använder den [officiella MongoDB .NET-drivrutinen](https://docs.mongodb.com/ecosystem/drivers/csharp/)kan du köra kommandon genom att anropa `RunCommand` metoden på ett `IMongoDatabase` objekt. Den här metoden kräver `Command<>` en implementering av den abstrakta klassen:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Mer information finns i [Snabbstart: Skapa en .NET-webbapp med hjälp av ett Azure Cosmos DB API för MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Använda MongoDB Java-drivrutinen


När du använder den [officiella MongoDB Java-drivrutinen](https://mongodb.github.io/mongo-java-driver/) `runCommand` kan `MongoDatabase` du köra kommandon genom att anropa metoden på ett objekt:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Mer information finns i [Snabbstart: Skapa en webbapp med hjälp av Azure Cosmos DB API för MongoDB och Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Använda drivrutinen MongoDB Node.js

När du använder den [officiella MongoDB Node.js-drivrutinen](https://mongodb.github.io/node-mongodb-native/) `command` kan `db` du köra kommandon genom att anropa metoden på ett objekt:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Mer information finns i [Snabbstart: Migrera en befintlig MongoDB Node.js-webbapp till Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Cassandra-API

När du utför åtgärder mot Azure Cosmos DB Cassandra API returneras RU-avgiften `RequestCharge`i den inkommande nyttolasten som ett fält med namnet . Du har flera alternativ för att hämta RU-avgiften.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)kan du hämta `Info` den inkommande `RowSet` nyttolasten under egenskapen för ett objekt:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Mer information finns i [Snabbstart: Skapa en Cassandra-app med hjälp av .NET SDK och Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)kan du hämta den `getExecutionInfo()` inkommande `ResultSet` nyttolasten genom att anropa metoden på ett objekt:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Mer information finns i [Snabbstart: Skapa en Cassandra-app med hjälp av Java SDK och Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Gremlin-API

När du använder Gremlin API har du flera alternativ för att hitta RU-förbrukningen för en åtgärd mot en Azure Cosmos-behållare. 

### <a name="use-drivers-and-sdk"></a>Använda drivrutiner och SDK

Rubriker som returneras av Gremlin-API:et mappas till anpassade statusattribut, som för närvarande visas av Gremlin .NET och Java SDK. Begäran debiteras under `x-ms-request-charge` nyckeln.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/)är statusattribut `StatusAttributes` tillgängliga under `ResultSet<>` objektets egenskap:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Mer information finns i [Snabbstart: Skapa ett .NET Framework- eller Core-program med hjälp av ett Azure Cosmos DB Gremlin API-konto](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)kan du hämta `statusAttributes()` statusattribut genom att anropa metoden på `ResultSet` objektet:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Mer information finns i [Snabbstart: Skapa en diagramdatabas i Azure Cosmos DB med hjälp av Java SDK](create-graph-java.md).

## <a name="table-api"></a>Tabell-API

För närvarande är den enda SDK som returnerar RU-avgiften för tabelloperationer [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Objektet `TableResult` exponerar `RequestCharge` en egenskap som fylls i av SDK när du använder den mot Azure Cosmos DB Table API:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Mer information finns i [Snabbstart: Skapa en tabell-API-app med hjälp av .NET SDK och Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du optimerar ru-förbrukningen finns i följande artiklar:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](optimize-cost-queries.md)
* [Skala etablerat dataflöde globalt](scaling-throughput.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Etablera dataflöde för en behållare](how-to-provision-container-throughput.md)
* [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md)
