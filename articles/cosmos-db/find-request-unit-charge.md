---
title: Hitta avgiften för begär ande enheten (RU) i Azure Cosmos DB
description: Lär dig hur du hittar avgiften för begär ande enheten (RU) för alla åtgärder som utförs mot en Azure Cosmos-behållare.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: bf109d3f15c9865a8e9ad1d27a1e8d320d172761
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261842"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Hitta enhets avgiften för begäran i Azure Cosmos DB

I den här artikeln beskrivs de olika sätt som du kan hitta för att köra den [begärda enheten](request-units.md) (ru) för alla åtgärder som utförs mot en behållare i Azure Cosmos dB. För närvarande kan du endast mäta den här förbrukningen genom att använda Azure Portal eller genom att granska svaret som skickats tillbaka från Azure Cosmos DB via en av SDK: erna.

## <a name="sql-core-api"></a>SQL-API (Core)

Om du använder SQL-API: et har du flera alternativ för att hitta RU-förbrukningen för en åtgärd mot en Azure Cosmos-behållare.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

För närvarande kan du bara hitta begär ande avgiften i Azure Portal för en SQL-fråga.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account) och mata in det med data, eller Välj ett befintligt Azure Cosmos-konto som redan innehåller data.

1. Gå till fönstret **datautforskaren** och välj den behållare som du vill arbeta med.

1. Välj **Ny SQL-fråga**.

1. Ange en giltig fråga och välj sedan **Kör fråga**.

1. Välj **fråga statistik** för att visa den faktiska begär ande avgiften för den begäran du utförde.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Skärm bild av en begäran om SQL-fråga i Azure Portal":::

### <a name="use-the-net-sdk"></a>Använda .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Objekt som returneras från [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) exponerar en `RequestCharge` egenskap:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Mer information finns i [snabb start: bygga en .NET-webbapp genom att använda ett SQL API-konto i Azure Cosmos DB](create-sql-api-dotnet.md).

---

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

Mer information finns i [snabb start: bygga ett Java-program med ett Azure Cosmos DB SQL API-konto](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Använd Node.js SDK

Objekt som returneras från [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) exponerar ett under `headers` objekt som mappar alla huvuden som returneras av det underliggande http-API: et. Begär ande avgiften är tillgänglig under `x-ms-request-charge` nyckeln:

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

Mer information finns i [snabb start: bygga en Node.js-app genom att använda ett Azure Cosmos DB SQL API-konto](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Använda Python SDK

`CosmosClient`Objektet från [python SDK](https://pypi.org/project/azure-cosmos/) visar en `last_response_headers` ord lista som mappar alla huvuden som returneras av det underliggande http-API: t för den senaste åtgärden som utfördes. Begär ande avgiften är tillgänglig under `x-ms-request-charge` nyckeln:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Mer information finns i [snabb start: bygga en python-app med ett Azure Cosmos DB SQL API-konto](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

Avgiften för RU visas med ett anpassat [databas kommando](https://docs.mongodb.com/manual/reference/command/) med namnet `getLastRequestStatistics` . Kommandot returnerar ett dokument som innehåller namnet på den senaste åtgärden som utfördes, dess begär ande avgift och dess varaktighet. Om du använder Azure Cosmos DB API för MongoDB har du flera alternativ för att hämta avgiften för RU.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

För närvarande kan du bara hitta begär ande avgiften i Azure Portal för en fråga.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account) och mata in det med data, eller Välj ett befintligt konto som redan innehåller data.

1. Gå till fönstret **datautforskaren** och välj den behållare som du vill arbeta med.

1. Välj **ny fråga**.

1. Ange en giltig fråga och välj sedan **Kör fråga**.

1. Välj **fråga statistik** för att visa den faktiska begär ande avgiften för den begäran du utförde.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Skärm bild av en MongoDB för förfrågningar om begär anden i Azure Portal":::

### <a name="use-the-mongodb-net-driver"></a>Använda MongoDB .NET-driv rutinen

När du använder den [officiella MongoDB .net-driv rutinen](https://docs.mongodb.com/ecosystem/drivers/csharp/)kan du köra kommandon genom att anropa `RunCommand` metoden för ett `IMongoDatabase` objekt. Den här metoden kräver en implementering av den `Command<>` abstrakta klassen:

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

Mer information finns i [snabb start: bygga en .NET-webbapp med hjälp av en Azure Cosmos DB-API för MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Använda Java-drivrutinen MongoDB


När du använder den [officiella MongoDB Java-drivrutinen](https://mongodb.github.io/mongo-java-driver/)kan du köra kommandon genom att anropa- `runCommand` metoden på ett `MongoDatabase` objekt:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Mer information finns i [snabb start: bygga en webbapp med hjälp av Azure Cosmos DB API för MongoDB och Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Använd Node.js driv rutinen för MongoDB

När du använder den [officiella MongoDB Node.js-drivrutinen](https://mongodb.github.io/node-mongodb-native/)kan du köra kommandon genom att anropa `command` metoden för ett `db` objekt:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Mer information finns i [snabb start: Migrera en befintlig MongoDB Node.js-webbapp till Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Cassandra-API

När du utför åtgärder mot Azure Cosmos DB API för Cassandra returneras RU-avgiften i inkommande nytto last som ett fält med namnet `RequestCharge` . Du har flera alternativ för att hämta RU-avgiften.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)kan du hämta inkommande nytto Last under `Info` ett `RowSet` objekts egenskap:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Mer information finns i [snabb start: bygga en Cassandra-app med hjälp av .NET SDK och Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)kan du hämta den inkommande nytto lasten genom att anropa `getExecutionInfo()` metoden på ett `ResultSet` objekt:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Mer information finns i [snabb start: bygga en Cassandra-app med hjälp av Java SDK och Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Gremlin-API

När du använder Gremlin-API: et har du flera alternativ för att hitta RU-förbrukningen för en åtgärd mot en Azure Cosmos-behållare. 

### <a name="use-drivers-and-sdk"></a>Använda driv rutiner och SDK

Huvuden som returneras av Gremlin-API: t mappas till anpassade status-attribut, som för närvarande finns i Gremlin .NET och Java SDK. Begär ande avgiften är tillgänglig under `x-ms-request-charge` nyckeln.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder [GREMLIN.NET SDK](https://www.nuget.org/packages/Gremlin.Net/)är attributet status tillgängligt under `StatusAttributes` `ResultSet<>` objektets egenskap:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Mer information finns i [snabb start: bygga en .NET Framework eller ett kärn program med hjälp av ett Azure Cosmos DB GREMLIN API-konto](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)kan du hämta attribut för status genom att anropa- `statusAttributes()` metoden på `ResultSet` objektet:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Mer information finns i [snabb start: skapa en graf-databas i Azure Cosmos dB med Java SDK](create-graph-java.md).

## <a name="table-api"></a>Tabell-API

För närvarande är den enda SDK som returnerar RU-avgiften för tabell åtgärder är [.net standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). `TableResult`Objektet visar en `RequestCharge` egenskap som är ifylld av SDK: n när du använder den mot Azure Cosmos DB tabell-API:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Mer information finns i [snabb start: bygga en tabell-API-app med hjälp av .NET SDK och Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar din RU-förbrukning finns i följande artiklar:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](optimize-cost-queries.md)
* [Skala etablerat dataflöde globalt](scaling-throughput.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Etablera data flöde för en behållare](how-to-provision-container-throughput.md)
* [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md)
