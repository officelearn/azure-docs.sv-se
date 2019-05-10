---
title: Hitta begäransenhet (RU) kostnad i Azure Cosmos DB
description: Lär dig att hitta begäransenhet (RU) kostnad för alla åtgärder som körs mot en Azure Cosmos-behållare.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: thweiss
ms.openlocfilehash: 730f0604d0c292764aaee6e0ffb8ef95d90be5f2
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410295"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Hitta kostnad för begäran-enhet i Azure Cosmos DB

Den här artikeln beskriver vi hur du hittar den [begäransenhet](request-units.md) (RU) förbrukningen för alla åtgärder som körs mot en behållare i Azure Cosmos DB. Du kan för närvarande kan mäta denna förbrukning endast med hjälp av Azure portal eller genom att granska svaret skickas tillbaka från Azure Cosmos DB via en av SDK: erna.

## <a name="sql-core-api"></a>SQL (kärna) API

Om du använder SQL-API: T har du flera alternativ för att hitta RU-förbrukningen för en åtgärd mot en Azure Cosmos-behållare.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

För närvarande kan du hitta kostnad för begäran i Azure-portalen endast för en SQL-fråga.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account) och feed med data, eller välja ett befintligt Azure Cosmos-konto som redan innehåller data.

1. Gå till den **Datautforskaren** rutan och välj sedan den behållare som du vill arbeta med.

1. Välj **ny SQL-fråga**.

1. Ange en giltig fråga och välj sedan **Kör fråga**.

1. Välj **fråga Stats** att visa den faktiska begäran-kostnaden för begäran som du körde.

![Skärmbild av en SQL-fråga kostnad för begäran i Azure portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Använd .NET SDK V2

Objekt som returneras från den [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) exponera en `RequestCharge` egenskap:

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

Mer information finns i [ Snabbstart: Skapa en .NET-webbapp med hjälp av ett SQL API-konto i Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Använda Java SDK

Objekt som returneras från den [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) exponera en `getRequestCharge()` metod:

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

Mer information finns i [ Snabbstart: Skapa en Java-program med hjälp av ett Azure Cosmos DB SQL API-konto](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Använda Node.js-SDK

Objekt som returneras från den [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) exponera en `headers` underobjekt som mappar alla rubriker som returneras av den underliggande HTTP-API: et. Kostnad för begäran är tillgängliga under den `x-ms-request-charge` nyckel:

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

Mer information finns i [ Snabbstart: Skapa en Node.js-app med hjälp av ett Azure Cosmos DB SQL API-konto](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Använda Python SDK

Den `CosmosClient` objekt från den [Python SDK](https://pypi.org/project/azure-cosmos/) Exponerar en `last_response_headers` ordlista som mappar alla rubriker som returneras av den underliggande http-API: et för den senaste åtgärden som utfördes. Kostnad för begäran är tillgängliga under den `x-ms-request-charge` nyckel:

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

Mer information finns i [ Snabbstart: Skapa en Python-app med hjälp av ett Azure Cosmos DB SQL API-konto](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

RU-avgift exponeras av en anpassad [databasen kommandot](https://docs.mongodb.com/manual/reference/command/) med namnet `getLastRequestStatistics`. Kommandot returnerar ett dokument som innehåller namnet på den senaste åtgärden som utfördes, dess kostnad för begäran och dess varaktighet. Om du använder Azure Cosmos DB API för MongoDB, har du flera alternativ för att hämta RU-avgiften.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

För närvarande kan du hitta kostnad för begäran i Azure-portalen endast för en fråga.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account) och feed med data, eller välja ett befintligt konto som redan innehåller data.

1. Gå till den **Datautforskaren** rutan och välj sedan den samling som du vill arbeta med.

1. Välj **ny fråga**.

1. Ange en giltig fråga och välj sedan **Kör fråga**.

1. Välj **fråga Stats** att visa den faktiska begäran-kostnaden för begäran som du körde.

![Skärmbild av en MongoDB-fråga kostnad för begäran i Azure portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Använd Mondodb .NET-drivrutinen

När du använder den [officiella Mondodb .NET-drivrutinen](https://docs.mongodb.com/ecosystem/drivers/csharp/), du kan köra kommandon genom att anropa den `RunCommand` metod på en `IMongoDatabase` objekt. Den här metoden kräver en implementering av den `Command<>` abstrakt klass:

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

Mer information finns i [ Snabbstart: Skapa en .NET-webbapp med hjälp av en Azure Cosmos DB-API för MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Använd Mondodb Java-drivrutinen


När du använder den [officiella Mondodb Java-drivrutinen](https://mongodb.github.io/mongo-java-driver/), du kan köra kommandon genom att anropa den `runCommand` metod på en `MongoDatabase` objekt:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Mer information finns i [ Snabbstart: Skapa en webbapp med hjälp av Azure Cosmos DB API för MongoDB och Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js-drivrutinen

När du använder den [officiella MongoDB Node.js-drivrutinen](https://mongodb.github.io/node-mongodb-native/), du kan köra kommandon genom att anropa den `command` metod på en `db` objekt:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Mer information finns i [ Snabbstart: Migrera en befintlig MongoDB Node.js-webbapp till Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Cassandra-API

När du utför åtgärder mot Azure Cosmos DB Cassandra-API, RU-kostnad returneras som ett fält med namnet i nyttolasten för inkommande `RequestCharge`. Du har flera alternativ för att hämta RU-avgiften.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder den [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/), du kan hämta inkommande nyttolasten under den `Info` egenskapen för en `RowSet` objekt:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

Mer information finns i [ Snabbstart: Skapa en Cassandra-app med hjälp av .NET SDK och Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder den [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), du kan hämta inkommande nyttolasten genom att anropa den `getExecutionInfo()` metod på en `ResultSet` objekt:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Mer information finns i [ Snabbstart: Skapa en Cassandra-app med hjälp av Java SDK och Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Gremlin-API

När du använder Gremlin-API kan har du flera alternativ för att hitta RU-förbrukningen för en åtgärd mot en Azure Cosmos-behållare. 

### <a name="use-drivers-and-sdk"></a>Använda drivrutiner och SDK

Rubriker som returneras av Gremlin-API: et mappas till anpassade attribut som för närvarande exponeras av Gremlin .NET och Java SDK. Kostnad för begäran är tillgängliga under den `x-ms-request-charge` nyckel.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

När du använder den [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/), status-attribut som är tillgängliga under den `StatusAttributes` egenskapen för den `ResultSet<>` objekt:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Mer information finns i [ Snabbstart: Skapa ett .NET Framework- eller Core-program med hjälp av ett Azure Cosmos DB Gremlin API-konto](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Använda Java SDK

När du använder den [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), du kan hämta status attribut genom att anropa den `statusAttributes()` metoden på den `ResultSet` objekt:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Mer information finns i [ Snabbstart: Skapa en grafdatabas i Azure Cosmos DB med hjälp av Java SDK](create-graph-java.md).

## <a name="table-api"></a>Tabell-API

Endast SDK: N som returnerar RU-kostnad för tabellåtgärder är för närvarande den [SDK för .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Den `TableResult` objekt visar ett `RequestCharge` egenskap som fylls av SDK när du använder det mot Azure Cosmos DB Table API:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Mer information finns i [ Snabbstart: Skapa en tabell-API-app med hjälp av .NET SDK och Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Nästa steg

Läs om hur du optimerar RU förbrukning i de här artiklarna:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
* [Optimera kostnader för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnader för frågan i Azure Cosmos DB](optimize-cost-queries.md)
* [Skala globalt etablerat dataflöde](scaling-throughput.md)
* [Etablera dataflöde på behållare och databaser](set-throughput.md)
* [Etablera dataflöde för en behållare](how-to-provision-container-throughput.md)
