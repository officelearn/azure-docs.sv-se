---
title: Partitionering och skalning i Azure Cosmos DB | Microsoft Docs
description: Läs mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitionera nycklar och hur du väljer rätt Partitionsnyckeln för ditt program.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d44d3933a132158a6dfca8201919eb72541f276
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163261"
---
# <a name="partitioning-in-azure-cosmos-db-using-the-sql-api"></a>Partitionering i Azure Cosmos DB med SQL API

[Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) är en globalt distribuerad databas för flera datamodeller-tjänst som utformats för att hjälpa dig att uppnå snabba och förutsägbara prestanda och skala smidigt tillsammans med ditt program när den växer. 

Den här artikeln innehåller en översikt över hur du arbetar med partitionering av Cosmos DB-behållare med SQL API. Se [partitionering och horisontell skalning](../cosmos-db/partition-data.md) en översikt över koncepten och bästa praxis för partitionering med alla API: er för Azure Cosmos DB. 

Kom igång med kod genom att hämta projektet från [Github](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

När du har läst den här artikeln kommer du att kunna besvara följande frågor:   

* Hur fungerar partitionering i Azure Cosmos DB?
* Hur konfigurerar jag partitionering i Azure Cosmos DB
* Vad är partitionsnycklar och hur jag välja rätt Partitionsnyckeln för mitt program?

Kom igång med kod genom att hämta projektet från [Azure Cosmos DB prestanda testning drivrutinen Sample](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Partitionsnycklar

I SQL-API anger du partition nyckeldefinition i form av en JSON-sökvägen. I följande tabell visas exempel på partitionen viktiga definitioner och de värden som motsvarar var och en. Partitionsnyckeln har angetts som en sökväg, till exempel `/department` representerar avdelningen egenskapen. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Partitionsnyckel</strong></p></td>
            <td valign="top"><p><strong>Beskrivning</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Motsvarar värdet för doc.department där dokumentet är objektet.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ Egenskaper/namn</p></td>
            <td valign="top"><p>Motsvarar värdet för doc.properties.name där dokumentet är objektet (kapslade egenskapen).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ ID</p></td>
            <td valign="top"><p>Motsvarar värdet för doc.id (id och partitions är samma egenskap).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ ”avdelningsnamn”</p></td>
            <td valign="top"><p>Motsvarar värdet för doc [”avdelningsnamn”] där dokumentet är objektet.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> Syntaxen för Partitionsnyckeln liknar sökvägsuttrycket för indexering princip sökvägar med den viktigaste skillnaden sökvägen motsvarar egenskapen i stället för värdet, dvs. det finns inga jokertecken i slutet. Till exempel, anger du/avdelning /? Om du vill indexera värden under avdelning, men ange /department som definitionen av en partition. Partitionsnyckeln indexeras implicit och kan inte uteslutas från att indexera med hjälp av åsidosättningar för indexering principen.
> 
> 

Nu ska vi titta på hur valet av partitionsnyckel påverkar programmets prestanda.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Arbeta med SDK: er för Azure Cosmos DB
Azure Cosmos DB lagt till stöd för automatisk partitionering med [REST API-version 2015-12-16](/rest/api/cosmos-db/). För att kunna skapa partitionerad behållare, måste du ladda ned SDK-versioner 1.6.0 eller senare eller senare på en av plattformarna som stöds SDK (.NET, Node.js, Java, Python, MongoDB). 

### <a name="creating-containers"></a>Skapa behållare
I följande exempel visas en .NET-kodfragment för att skapa en behållare för att lagra telemetri enhetsdata för 20 000 programbegäran per sekund för dataflödet. SDK anger OfferThroughput-värde (som i sin tur anger den `x-ms-offer-throughput` begäranderubriken i REST API). Här du anger den `/deviceId` som partitionsnyckel. Val av partitionsnyckel sparas tillsammans med resten av metadata för behållaren som namn och indexeringsprincip.

I det här exemplet som du valt `deviceId` eftersom du vet att (a) det finns ett stort antal enheter, skriver kan vara jämnt över partitionerna och så att du kan skala databasen för att mata in stora mängder data och (b) många av förfrågningarna som hämtning den senaste avläsningen för en enhet är begränsade till en enda deviceId och kan hämtas från en enda partition.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Den här metoden gör ett REST API-anrop till Cosmos DB och tjänsten etablerar ett antal partitioner baserat på det begärda genomströmningen. Du kan ändra dataflödet för en behållare eller en uppsättning behållare som din prestanda behov ändras. 

### <a name="reading-and-writing-items"></a>Läsning och skrivning av objekt
Nu kan vi infoga data i Cosmos DB. Här är ett exempel på klass som innehåller en enhet som läser och ett anrop till CreateDocumentAsync att infoga en ny enhet som läser i en behållare. Följande är ett exempel kodblock som utnyttjar SQL API:

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

Nu ska vi läsa objekt efter dess partitionsnyckel och id, uppdatera den och sedan som ett sista steg, ta bort den efter partitionsnyckel och ID: t. Läsningar inkluderar ett PartitionKey-värde (som motsvarar den `x-ms-documentdb-partitionkey` begäranderubriken i REST API).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>Fråga partitionerade behållare
När du begär data i partitionerade behållare dirigerar Cosmos DB automatiskt frågan till de partitioner som motsvarar partitionsnyckelvärdena som angetts i filtret (om det finns några). Den här frågan dirigeras till exempel enbart till den partition som innehåller partitionsnyckeln XMS-0001.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
Följande fråga har inget filter på partitionsnyckeln (DeviceId) och är utspridd till alla partitioner där den körs mot partitionens index. Du måste ange EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` i REST-API) ha SDK, för att köra en fråga över partitioner.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB stöder [mängdfunktioner](how-to-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, och `AVG` över partitionerad behållare med hjälp av SQL som börjar med SDK: er 1.12.0 och senare. Frågor måste innehålla en enda mängdoperator och måste innehålla ett enda värde i projektionen.

### <a name="parallel-query-execution"></a>Parallell frågekörning
I Cosmos DB SDK: er 1.9.0 och högre parallell körning supportalternativ som gör det möjligt att utföra frågor med låg svarstid mot partitionerade samlingar, även när de behöver vidröra ett stort antal partitioner. Följande fråga är till exempel konfigurerad för att köras parallellt över partitioner.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Du kan hantera parallell frågekörning genom att justera följande parametrar:

* Genom att ange `MaxDegreeOfParallelism`, du kan styra graden av parallellitet d.v.s. det maximala antalet samtidiga nätverksanslutningar till behållarens partitioner. Om du anger den här egenskapen till -1 hanteras graden av parallellitet av SDK:n. Om den `MaxDegreeOfParallelism` är inte angiven eller har angetts till 0, vilket är standardvärdet så finns det en enda nätverksanslutning till behållarens partitioner.
* Genom att ange `MaxBufferedItemCount`, kan du avväga frågesvarstider och minnesanvändning på klientsidan. Om du utelämnar den här parametern eller anger den här egenskapen till-1, hanteras antalet objekt som buffras under parallell frågekörning av SDK: N.

Med samma status för samlingen, returnerar en parallell fråga resultat i samma ordning som vid seriell körning. När du utför en fråga i över partitioner som inkluderar sortering (ORDER BY och/eller TOP), Azure Cosmos DB SDK skickar frågan parallellt över partitionerna och sammanfogar delvis sorterade resulterar i klientsidan för att skapa globalt sorterade resultat.

### <a name="executing-stored-procedures"></a>Köra lagrade procedurer
Du kan också köra atomiska transaktioner mot dokument med samma enhets-ID, till exempel om du hanterar aggregat eller det senaste tillståndet för en enhet i ett enskilt objekt. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
I nästa avsnitt, kan du titta på hur du kan flytta till partitionerad behållare från en partition behållare.

## <a name="next-steps"></a>Nästa steg
Den här artikeln visas en översikt över hur du arbetar med partitionering av Azure Cosmos DB-behållare med SQL API. Se även [partitionering och horisontell skalning](../cosmos-db/partition-data.md) en översikt över koncepten och bästa praxis för partitionering med alla API: er för Azure Cosmos DB. 

* Utföra skalnings- och prestandatester med Azure Cosmos DB. Se [prestanda och Skalningstester med Azure Cosmos DB](performance-testing.md) för ett exempel.
* Komma igång med programmeringen med den [SDK: er](sql-api-sdk-dotnet.md) eller [REST API](/rest/api/cosmos-db/)
* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md)

