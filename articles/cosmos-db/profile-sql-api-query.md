---
title: Hämta SQL-fråga prestanda och utförande-mått
description: Lär dig hur du hämtar körningsstatistik för SQL-fråga och SQL-frågeprestanda för Azure Cosmos DB-begäranden-profilen.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481571"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Hämta körningsstatistik för SQL-fråga och analysera prestanda för frågor med .NET SDK

Den här artikeln beskriver vi hur du profilera SQL-frågeprestanda i Azure Cosmos DB. Den här Profileringen kan göras med hjälp av `QueryMetrics` hämtas från .NET-SDK och beskrivs här. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) är en starkt typifierad objekt med information om körningen av backend-frågan. De här måtten finns dokumenterade i detalj i de [finjustera frågeprestanda](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) artikeln.

## <a name="set-the-feedoptions-parameter"></a>Ange parametern FeedOptions

Alla överlagringar för [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) ta i en valfri [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) parametern. Det här alternativet är det låter Frågekörningen justerade och som innehåller parametrar. 

Om du vill samla in körningsstatistik för Sql-fråga måste du ange parametern [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) i den [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) till `true`. Ange `PopulateQueryMetrics` till SANT gör det så att den `FeedResponse` innehåller relevant `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Hämta fråga mått med AsDocumentQuery()
Följande kodexempel visar hur du hämtar mått när du använder [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) metod:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Sammanställning av QueryMetrics

Observera att det fanns flera anrop till i det föregående avsnittet [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) metod. Varje anropet returnerade en `FeedResponse` objekt som har en ordlista med `QueryMetrics`; en för varje fortsättning av frågan. I följande exempel visas hur du aggregera dessa `QueryMetrics` med hjälp av LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Gruppering fråga mått med partitions-ID

Du kan gruppera de `QueryMetrics` med partitions-ID. Gruppering av partitions-ID kan du se om en specifik Partition som orsakar prestandaproblem i förhållande till andra. I följande exempel visas hur du vill gruppera `QueryMetrics` med LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ på DocumentQuery

Du kan också hämta den `FeedResponse` från en LINQ-frågan med hjälp av den `AsDocumentQuery()` metoden:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Dyra frågor

Du kan avbilda programbegäran som förbrukas av varje fråga om du vill undersöka dyra frågor eller frågor som förbrukar stora dataflöden. Du kan hämta kostnad för begäran med hjälp av den [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) -egenskapen i `FeedResponse`. Läs mer om hur du hämtar kostnad för begäran med hjälp av Azure-portalen och olika SDK: er i [hitta kostnaden för begäran enheten](find-request-unit-charge.md) artikeln.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Hämta körningstid för fråga

Vid beräkning av den tid som krävs för att köra en fråga på klientsidan, se till att du bara ta tid att anropa den `ExecuteNextAsync` metod och inte andra delar av din kodbas. Bara dessa anrop hjälper dig att beräkna hur lång tid körningen av frågan tog som visas i följande exempel:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Skanna frågor (ofta långsam och dyr)

En genomsökning fråga refererar till en fråga som inte hanteras av index på grund av som många dokument har lästs in innan den returneras resultatet.

Nedan visas ett exempel på en sökning-fråga:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtret för den här frågan använder systemfunktion övre som inte hanteras från indexet. Kör den här frågan mot en stor samling produceras följande fråga mått för den första fortsättningen:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Observera följande värden från fråga mått utdata:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Den här frågan läsa in 60,951 dokument som summeras 399,998,938 byte. Läser in detta antal byte leder till hög kostnad eller begäran kostnaden för enheten. Det kan också tar lång tid att köra frågan, vilket blir tydligt med egenskapen total tid:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Vilket innebär att som frågan tog 4.5 sekunder att köra (och det var bara en fortsättning).

Undvik att använda övre i filtret för att optimera den här exempelfråga. I stället när dokument har skapats eller uppdaterats, den `c.description` värden måste infogas i alla versaler. Frågan blir: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Den här frågan kan nu hanteras från indexet.

Läs mer om att justera prestanda för frågor i den [finjustera frågeprestanda](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) artikeln.

## <a id="References"></a>Referenser

- [Azure Cosmos DB SQL-specifikation](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Nästa steg

- [Justera prestanda för frågor](sql-api-query-metrics.md)
- [Indexering, översikt](index-overview.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
