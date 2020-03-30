---
title: Hämta SQL-frågeprestanda & körningsmått
description: Lär dig hur du hämtar mått för SQL-frågekörning och profil-SQL-frågeprestanda för Azure Cosmos DB-begäranden.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998370"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Hämta mått för SQL-frågekörning och analysera frågeprestanda med .NET SDK

I den här artikeln beskrivs hur du profilerar SQL-frågeprestanda på Azure Cosmos DB. Denna profilering kan `QueryMetrics` göras med hjälp av hämtas från .NET SDK och beskrivs här. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) är ett starkt maskinskrivet objekt med information om körningen av backend-frågefråge. Dessa mått dokumenteras mer i detalj i artikeln [Justera frågeprestanda.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="set-the-feedoptions-parameter"></a>Ange parametern FeedOptions

Alla överbelastningar för [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) tar in en valfri [FeedOptions-parameter.](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) Det här alternativet är det som gör att frågekörningen kan justeras och parameteriseras. 

Om du vill samla in mätvärdena för sql-frågekörning måste du ange `true`parametern [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) i [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) till . Om `PopulateQueryMetrics` du ställer in true `FeedResponse` blir det `QueryMetrics`så att den innehåller relevant . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Hämta frågemått med AsDocumentQuery()
Följande kodexempel visar hur du hämtar mått när du använder [AsDocumentQuery()-metoden:](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)

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
## <a name="aggregating-querymetrics"></a>Samla frågemetri

I föregående avsnitt märker du att det fanns flera anrop till [metoden ExecuteNextAsync.](https://msdn.microsoft.com/library/azure/dn850294.aspx) Varje samtal returnerade ett `FeedResponse` objekt `QueryMetrics`som har en ordlista med ; en för varje fortsättning av frågan. I följande exempel visas `QueryMetrics` hur du aggregerar dessa med LINQ:

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

## <a name="grouping-query-metrics-by-partition-id"></a>Gruppera frågemått efter partitions-ID

Du kan `QueryMetrics` gruppera partitions-ID:t. Genom att gruppera efter partitions-ID kan du se om en viss partition orsakar prestandaproblem jämfört med andra. I följande exempel visas `QueryMetrics` hur du grupperar med LINQ:

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

Du kan också `FeedResponse` hämta från en `AsDocumentQuery()` LINQ-fråga med metoden:

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

Du kan samla in de begärandeenheter som används av varje fråga för att undersöka dyra frågor eller frågor som förbrukar högt dataflöde. Du kan få begärssaneringen genom `FeedResponse`att använda egenskapen [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) i . Mer information om hur du hämtar begäranden med Azure-portalen och olika SDK:er finns i artikeln [om begäranhetsankostnad.](find-request-unit-charge.md)

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

## <a name="get-the-query-execution-time"></a>Hämta tid för körning av frågor

När du beräknar den tid som krävs för att köra en fråga `ExecuteNextAsync` på klientsidan ska du se till att du bara tar med tiden att anropa metoden och inte andra delar av kodbasen. Just dessa samtal hjälper dig att beräkna hur lång tid frågekörningen tog som visas i följande exempel:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Skanna frågor (ofta långsamma och dyra)

En skanningsfråga refererar till en fråga som inte har hanterats av indexet, på grund av vilken många dokument läses in innan resultatuppsättningen returneras.

Nedan följer ett exempel på en skanningsfråga:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Den här frågans filter använder systemfunktionen UPPER, som inte visas från indexet. När du körde den här frågan mot en stor samling producerades följande frågemått för den första fortsättningen:

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

Observera följande värden från utdata för frågemått:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Den här frågan läste in 60 951 dokument, vilket uppgick till 399 998 938 byte. Om du läser in så här många byte leder det till hög kostnad eller en begäranhetsavgift. Det tar också lång tid att köra frågan, vilket är tydligt med egenskapen total tid:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Vilket innebär att frågan tog 4,5 sekunder att köra (och detta var bara en fortsättning).

Om du vill optimera den här exempelfrågan undviker du användningen av UPPER i filtret. När dokument skapas eller uppdateras måste `c.description` värdena infogas i versaler. Frågan blir då: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Den här frågan kan nu visas från indexet.

Mer information om hur du justerar frågeprestanda finns i artikeln [Justera frågeprestanda.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="references"></a><a id="References"></a>Referenser

- [Azure Cosmos DB SQL-specifikation](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [Json](https://json.org/)
- [Linq](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Nästa steg

- [Justera frågeprestanda](sql-api-query-metrics.md)
- [Översikt över indexering](index-overview.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
