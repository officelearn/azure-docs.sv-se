---
title: Hitta begäransenhet (RU) kostnad i Azure Cosmos DB
description: Lär dig att hitta kostnaden för begäran om enheten för alla åtgärder som körs mot en Azure Cosmos-behållare
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2019
ms.author: thweiss
ms.openlocfilehash: e3175ee136057c695ceef3cd1976b447a529c803
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59053169"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Hitta begäransenhet (RU) kostnad i Azure Cosmos DB

Den här artikeln beskrivs olika sätt att hitta den [begäransenhet](request-units.md) förbrukningen för alla åtgärder som körs mot en Azure Cosmos-behållare. Det går för närvarande att mäta den här användning med hjälp av Azure portal eller genom att kontrollera de svar som skickas tillbaka från Azure Cosmos DB via en av SDK: erna.

## <a name="core-api"></a>Core API

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Azure-portalen kan för närvarande du hitta kostnad för begäran för en SQL-fråga bara.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-sql-api-dotnet.md#create-account) och feed med data, eller välja ett befintligt konto som redan innehåller data.

1. Öppna den **Datautforskaren** rutan och välj den behållare som du vill arbeta med.

1. Klicka på **ny SQL-fråga**.

1. Ange en giltig fråga och klicka sedan på **Kör fråga**.

1. Klicka på **fråga Stats** att visa den faktiska begäran kostnaden för den begäran som du precis har genomfört.

![Skärmbild av SQL-fråga begäran avgift på Azure portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Använd .NET SDK V2

Objekt som returneras från den [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (se [den här snabbstarten](create-sql-api-dotnet.md) rörande användningen) exponera en `RequestCharge` egenskapen.

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

### <a name="use-the-java-sdk"></a>Använda Java SDK

Objekt som returneras från den [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (se [den här snabbstarten](create-sql-api-java.md) rörande användningen) exponera en `getRequestCharge()` metod.

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

### <a name="use-the-nodejs-sdk"></a>Använda Node.js-SDK

Objekt som returneras från den [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (se [den här snabbstarten](create-sql-api-nodejs.md) rörande användningen) exponera en `headers` underobjekt som mappar alla rubriker som returneras av den underliggande HTTP-API: et. Kostnad för begäran är tillgängliga under den `x-ms-request-charge` nyckel.

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

### <a name="use-the-python-sdk"></a>Använda Python SDK

Den `CosmosClient` objekt från den [Python SDK](https://pypi.org/project/azure-cosmos/) (se [den här snabbstarten](create-sql-api-python.md) rörande användningen) visar en `last_response_headers` ordlista som mappar alla rubriker som returneras av den underliggande http-API: et för den senaste åtgärden som utfördes. Kostnad för begäran är tillgängliga under den `x-ms-request-charge` nyckel.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

Begär avgiften för lyssnarenheten exponeras av en anpassad [databasen kommandot](https://docs.mongodb.com/manual/reference/command/) med namnet `getLastRequestStatistics`. Det här kommandot returnerar ett dokument som innehåller namnet på den senaste åtgärden som utfördes, dess kostnad för begäran och dess varaktighet.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Azure-portalen kan för närvarande du hitta kostnad för begäran för enbart en fråga.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-mongodb-dotnet.md#create-a-database-account) och feed med data, eller välja ett befintligt konto som redan innehåller data.

1. Öppna den **Datautforskaren** rutan och välj den samling som du vill arbeta med.

1. Klicka på **ny fråga**.

1. Ange en giltig fråga och klicka sedan på **Kör fråga**.

1. Klicka på **fråga Stats** att visa den faktiska begäran kostnaden för den begäran som du precis har genomfört.

![Skärmbild av MongoDB fråga begäran avgift på Azure portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Använd Mondodb .NET-drivrutinen

När du använder den [officiella Mondodb .NET-drivrutinen](https://docs.mongodb.com/ecosystem/drivers/csharp/) (se [den här snabbstarten](create-mongodb-dotnet.md) rörande användningen), kommandon kan köras genom att anropa den `RunCommand` metod på en `IMongoDatabase` objekt. Den här metoden kräver en implementering av den `Command<>` abstrakt klass.

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

### <a name="use-the-mongodb-java-driver"></a>Använd Mondodb Java-drivrutinen

När du använder den [officiella Mondodb Java-drivrutinen](http://mongodb.github.io/mongo-java-driver/) (se [den här snabbstarten](create-mongodb-java.md) rörande användningen), kommandon kan köras genom att anropa den `runCommand` metod på en `MongoDatabase` objekt.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js-drivrutinen

När du använder den [officiella MongoDB Node.js-drivrutinen](https://mongodb.github.io/node-mongodb-native/) (se [den här snabbstarten](create-mongodb-nodejs.md) rörande användningen), kommandon kan köras genom att anropa den `command` metod på en `Db` objekt.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>Cassandra-API

När du utför åtgärder mot Azure Cosmos DB Cassandra-API, begäran avgiften för lyssnarenheten returneras som ett fält med namnet i nyttolasten för inkommande `RequestCharge`.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder den [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) (se [den här snabbstarten](create-cassandra-dotnet.md) rörande användningen), inkommande nyttolasten kan hämtas den `Info` egenskapen för en `RowSet` objekt.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder den [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (se [den här snabbstarten](create-cassandra-java.md) rörande användningen), inkommande nyttolasten kan hämtas genom att anropa den `getExecutionInfo()` metod på en `ResultSet` objekt.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>Gremlin-API

### <a name="use-drivers-and-sdk"></a>Använda drivrutiner och SDK

Rubriker som returneras av Gremlin-API: et mappas till anpassade attribut som exponeras för närvarande av Gremlin .NET och Java SDK. Kostnad för begäran är tillgängliga under den `x-ms-request-charge` nyckel.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder den [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) (se [den här snabbstarten](create-graph-dotnet.md) rörande användningen), status attributen är tillgängliga under den `StatusAttributes` egenskapen för den `ResultSet<>` objekt.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder den [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (se [den här snabbstarten](create-graph-java.md) rörande användningen), status attribut kan hämtas genom att anropa den `statusAttributes()` metoden på den `ResultSet` objekt.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>Tabell-API

Endast SDK för närvarande returnerar begäran enhet kostar tabellåtgärder är den [SDK för .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (se [den här snabbstarten](create-table-dotnet.md) rörande användningen). Den `TableResult` objekt visar ett `RequestCharge` egenskap som fylls av SDK när den används mot Azure Cosmos DB Table API.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för att lära dig om hur du optimerar dina enhet användningen av begäran:

* [Optimera kostnader för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnader för frågan i Azure Cosmos DB](optimize-cost-queries.md)