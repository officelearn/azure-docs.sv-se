---
title: SQL-frågemått för Azure Cosmos DB SQL API
description: Lär dig mer om hur du instrumenterar och felsöker SQL-frågeprestanda för Azure Cosmos DB-begäranden.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: ae1773ec1d470b9cff2efb00c200427b7b4c2fb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614817"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Justera frågeprestanda med Azure Cosmos DB

Azure Cosmos DB tillhandahåller ett [SQL API för att fråga data](how-to-sql-query.md), utan att kräva schema eller sekundära index. Den här artikeln innehåller följande information för utvecklare:

* Information på hög nivå om hur Azure Cosmos DB:s SQL-frågekörning fungerar
* Information om frågebegäran och svarsrubriker och klient-SDK-alternativ
* Tips och metodtips för frågeprestanda
* Exempel på hur du använder SQL-körningsstatistik för att felsöka frågeprestanda

## <a name="about-sql-query-execution"></a>Om KÖRNING AV SQL-frågor

I Azure Cosmos DB lagrar du data i behållare som kan växa till valfri [lagringsstorlek eller begära dataflöde](partition-data.md). Azure Cosmos DB skalar sömlöst data över fysiska partitioner under omfattar för att hantera datatillväxt eller ökning av etablerat dataflöde. Du kan utfärda SQL-frågor till valfri behållare med REST API eller någon av de [SQL SDK:er](sql-api-sdk-dotnet.md)som stöds .

En kort översikt över partitionering: du definierar en partitionsnyckel som "stad", som avgör hur data delas upp över fysiska partitioner. Data som tillhör en enda partitionsnyckel (till exempel "stad" == "Seattle") lagras i en fysisk partition, men vanligtvis har en enda fysisk partition flera partitionsnycklar. När en partition når sin lagringsstorlek delar tjänsten sömlöst upp partitionen i två nya partitioner och delar upp partitionsnyckeln jämnt över dessa partitioner. Eftersom partitioner är tillfälliga använder API:erna en abstraktion av ett "partitionsnyckelintervall", vilket betecknar intervallen för partitionsnyckel hashar. 

När du utfärdar en fråga till Azure Cosmos DB utför SDK följande logiska steg:

* Tolka SQL-frågan för att fastställa frågekörningsplanen. 
* Om frågan innehåller ett filter mot `SELECT * FROM c WHERE c.city = "Seattle"`partitionsnyckeln, till exempel, dirigeras den till en enda partition. Om frågan inte har något filter på partitionsnyckeln körs den i alla partitioner och resultaten slås samman på klientsidan.
* Frågan körs inom varje partition i serie eller parallell, baserat på klientkonfiguration. I varje partition kan frågan göra en eller flera rundresor beroende på frågans komplexitet, konfigurerad sidstorlek och etablerat dataflöde för samlingen. Varje körning returnerar antalet [begärandeenheter](request-units.md) som förbrukas av frågekörning och eventuellt statistik för frågekörning. 
* SDK utför en summering av frågeresultaten över partitioner. Om frågan till exempel innehåller en ORDER BY över partitioner, sammanfogas resultat från enskilda partitioner för att returnera resultat i globalt sorterad ordning. Om frågan är en aggregering som `COUNT`summeras antalet från enskilda partitioner för att skapa det totala antalet.

SDK:erna innehåller olika alternativ för körning av frågor. I .NET är dessa alternativ till `FeedOptions` exempel tillgängliga i klassen. I följande tabell beskrivs dessa alternativ och hur de påverkar körningstiden för frågor. 

| Alternativ | Beskrivning |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Måste vara inställt på true för alla frågor som måste köras över mer än en partition. Detta är en explicit flagga som gör att du kan göra medvetna prestanda kompromisser under utvecklingstiden. |
| `EnableScanInQuery` | Måste vara inställt på true om du har valt bort indexering, men vill köra frågan via en genomsökning ändå. Endast tillämpligt om indexeringen för den begärda filtersökvägen är inaktiverad. | 
| `MaxItemCount` | Det maximala antalet objekt som ska returneras per tur och retur till servern. Genom att ange -1 kan du låta servern hantera antalet objekt. Du kan också sänka det här värdet om du bara vill hämta ett litet antal objekt per tur och retur. 
| `MaxBufferedItemCount` | Det här är ett alternativ på klientsidan och används för att begränsa minnesförbrukningen när du utför order mellan partitionering GENOM. Ett högre värde bidrar till att minska svarstiden för sortering mellan partitioner. |
| `MaxDegreeOfParallelism` | Hämtar eller anger antalet samtidiga åtgärder som körs på klientsidan under parallell körning av frågor i Azure Cosmos-databastjänsten. Ett positivt egenskapsvärde begränsar antalet samtidiga operationer till det inställda värdet. Om den är inställd på mindre än 0 bestämmer systemet automatiskt antalet samtidiga åtgärder som ska köras. |
| `PopulateQueryMetrics` | Möjliggör detaljerad loggning av statistik över tid som tillbringats i olika faser av frågekörning som kompileringstid, indexlooptid och dokumentinläsningstid. Du kan dela utdata från frågestatistik med Azure Support för att diagnostisera problem med frågeprestanda. |
| `RequestContinuation` | Du kan återuppta frågekörningen genom att skicka den ogenomskinliga fortsättningstoken som returneras av en fråga. Fortsättningstoken kapslar in alla tillstånd som krävs för körning av frågor. |
| `ResponseContinuationTokenLimitInKb` | Du kan begränsa den maximala storleken på fortsättningstoken som returneras av servern. Du kan behöva ange detta om programvärden har begränsningar för svarshuvudets storlek. Om du anger detta kan den totala varaktigheten och ru:er som förbrukas för frågan öka.  |

Låt oss till exempel ta en exempelfråga på partitionsnyckel som begärs på en samling med `/city` som partitionsnyckel och etablerad med 100 000 RU/s dataflöde. Du begär den `CreateDocumentQuery<T>` här frågan med hjälp av .NET på följande sätt:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

SDK-kodavsnittet som visas ovan motsvarar följande REST API-begäran:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Varje sida för frågekörning motsvarar `POST` ett `Accept: application/query+json` REST API med huvudet och SQL-frågan i brödtexten. Varje fråga gör en eller flera rundresor till servern med `x-ms-continuation` token ekade mellan klienten och servern för att återuppta körningen. Konfigurationsalternativen i FeedOptions skickas till servern i form av begäranden. Till exempel `MaxItemCount` motsvarar `x-ms-max-item-count`. 

Begäran returnerar följande (trunkerade för läsbarhet) svar:

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

De nyckelsvarsrubriker som returneras från frågan innehåller följande:

| Alternativ | Beskrivning |
| ------ | ----------- |
| `x-ms-item-count` | Antalet artiklar som returneras i svaret. Detta beror på det `x-ms-max-item-count`angivna , antalet artiklar som kan passa inom den maximala svarsnyttolastens storlek, etablerat dataflöde och körningstid för frågor. |  
| `x-ms-continuation:` | Fortsättningstoken för att återuppta körningen av frågan, om ytterligare resultat är tillgängliga. | 
| `x-ms-documentdb-query-metrics` | Frågestatistiken för körningen. Detta är en avgränsad sträng som innehåller statistik över tid som tillbringats i de olika faserna av frågekörningen. Returnerad `x-ms-documentdb-populatequerymetrics` om är `True`inställd på . | 
| `x-ms-request-charge` | Antalet [begärandeenheter](request-units.md) som förbrukas av frågan. | 

Mer information om rest API-begäranden och alternativ finns i [Fråga resurser med REST API](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Metodtips för frågeprestanda
Följande är de vanligaste faktorerna som påverkar Azure Cosmos DB-frågeprestanda. Vi gräver djupare i vart och ett av dessa ämnen i den här artikeln.

| Faktor | Tips | 
| ------ | -----| 
| Etablerat dataflöde | Mät RU per fråga och se till att du har det nödvändiga etablerade dataflödet för dina frågor. | 
| Partitionerings- och partitionsnycklar | Favorfrågor med partitionsnyckelvärdet i filtersatsen för låg svarstid. |
| SDK- och frågealternativ | Följ SDK:s metodtips som direktanslutning och finjustera alternativ för körning av frågor på klientsidan. |
| Svarstid för nätverk | Ta hänsyn till nätverksomkostnader i mätningen och använd API:er för flera målpunkter för att läsa från närmaste region. |
| Indexeringsprincip | Kontrollera att du har de indexeringsvägar/principen som krävs för frågan. |
| Frågekörningsmått | Analysera frågekörningsmåtten för att identifiera potentiella omskrivningar av fråge- och dataformer.  |

### <a name="provisioned-throughput"></a>Etablerat dataflöde
I Cosmos DB skapar du behållare med data, var och en med reserverat dataflöde uttryckt i begäranheter (RU) per sekund. En läsning av ett 1 KB-dokument är 1 RU och varje åtgärd (inklusive frågor) normaliseras till ett fast antal ru:er baserat på dess komplexitet. Om du till exempel har 1000 RU/s som har etablerats `SELECT * FROM c WHERE c.city = 'Seattle'` för din behållare, och du har en fråga som som förbrukar 5 ru: er, kan du utföra (1000 RU/s) / (5 RU/query) = 200 frågor/s sådana frågor per sekund. 

Om du skickar in fler än 200 frågor/sek startar tjänsten hastighetsbegränsande inkommande begäranden över 200/s. SDK:erna hanterar automatiskt det här fallet genom att utföra ett backoff/retry, därför kan du märka en högre svarstid för dessa frågor. Om du ökar det etablerade dataflödet till det värde som krävs förbättras frågefördröjningen och dataflödet. 

Mer information om begäranheter finns i [Begär enheter](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partitionerings- och partitionsnycklar
Med Azure Cosmos DB utförs vanligtvis frågor i följande ordning från snabbast/mest effektiva till långsammare/mindre effektivt. 

* HÄMTA på en enda partitionsnyckel och objektnyckel
* Fråga med en filtersats på en enda partitionsnyckel
* Fråga utan likhets- eller intervallfiltersats på någon egenskap
* Fråga utan filter

Frågor som behöver konsultera alla partitioner behöver högre svarstid och kan använda högre ru: er. Eftersom varje partition har automatisk indexering mot alla egenskaper kan frågan hanteras effektivt från indexet i det här fallet. Du kan göra frågor som sträcker sig snabbare genom att använda parallelliseringsalternativen.

Mer information om partitionering och partitionsnycklar finns [i Partitionering i Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK- och frågealternativ
Se [Prestandatips](performance-tips.md) och [prestandatestning](performance-testing.md) för hur du får bästa prestanda på klientsidan från Azure Cosmos DB. Detta inkluderar att använda de senaste SDK:erna, konfigurera plattformsspecifika konfigurationer som standardantalet anslutningar, frekvensen av skräpinsamling och använda lätta anslutningsalternativ som Direct/TCP. 


#### <a name="max-item-count"></a>Maximalt antal objekt
För frågor kan värdet `MaxItemCount` för ha en betydande inverkan på frågetid från på på sluten tid. Varje tur och retur till servern returnerar inte `MaxItemCount` mer än antalet objekt i (Standard på 100 objekt). Om du anger detta till ett högre värde (-1 är det högsta och rekommenderade) förbättras frågevaraktigheten totalt sett genom att begränsa antalet tur- och returresor mellan server och klient, särskilt för frågor med stora resultatuppsättningar.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximal grad av parallellism
För frågor, justera `MaxDegreeOfParallelism` för att identifiera de bästa konfigurationerna för ditt program, särskilt om du utför korspartitionsfrågor (utan filter på partitionsnyckelvärdet). `MaxDegreeOfParallelism`styr det maximala antalet parallella uppgifter, dvs. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Låt oss anta att
* D = Maximalt antal parallella aktiviteter (= totalt antal processorer i klientdatorn)
* P = Användarspecificerat maximalt antal parallella uppgifter
* N = Antal partitioner som måste besökas för att svara på en fråga

Följande är konsekvenserna av hur parallella frågor skulle bete sig för olika värden i P.
* (P == 0) => serieläge
* (P == 1) => Högst en aktivitet
* (P > 1) => Min (P, N) parallella uppgifter 
* (P < 1) => Min (N, D) parallella uppgifter

För SDK-versionsanteckningar och information om implementerade klasser och metoder finns [i SQL SDK:er](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Svarstid för nätverk
Se [Azure Cosmos DB global distribution](tutorial-global-distribution-sql-api.md) för hur du konfigurerar global distribution och ansluter till närmaste region. Nätverksfördröjning har en betydande inverkan på frågeprestanda när du behöver göra flera rundresor eller hämta en stor resultatuppsättning från frågan. 

I avsnittet om frågekörningsmått beskrivs hur du hämtar `totalExecutionTimeInMs`serverkörningstiden för frågor ( ), så att du kan skilja mellan tid som tillbringats i frågekörning och tid som spenderas i nätverkstransit.

### <a name="indexing-policy"></a>Indexeringspolicy
Se [Konfigurera indexeringsprincipen](index-policy.md) för indexering av sökvägar, typer och lägen och hur de påverkar körningen av frågor. Som standard använder indexeringsprincipen Hash-indexering för strängar, vilket är effektivt för likhetsfrågor, men inte för intervallfrågor/ordning efter frågor. Om du behöver intervallfrågor för strängar rekommenderar vi att du anger indextypen Område för alla strängar. 

Som standard kommer Azure Cosmos DB att tillämpa automatisk indexering på alla data. För högpresterande skärscenarier bör du överväga att utesluta sökvägar eftersom detta minskar RU-kostnaden för varje skäroperation. 

## <a name="query-execution-metrics"></a>Frågekörningsmått
Du kan hämta detaljerade mått för frågekörning genom att skicka det valfria `x-ms-documentdb-populatequerymetrics` huvudet (i`FeedOptions.PopulateQueryMetrics` .NET SDK). Värdet som returneras i `x-ms-documentdb-query-metrics` har följande nyckel-värde-par avsedda för avancerad felsökning av frågekörning. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Mått | Enhet | Beskrivning | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | millisekunder | Tid för körning av frågor | 
| `queryCompileTimeInMs` | millisekunder | Tid för frågekompilering  | 
| `queryLogicalPlanBuildTimeInMs` | millisekunder | Dags att skapa logisk frågeplan | 
| `queryPhysicalPlanBuildTimeInMs` | millisekunder | Dags att skapa fysisk frågeplan | 
| `queryOptimizationTimeInMs` | millisekunder | Tid för att optimera frågan | 
| `VMExecutionTimeInMs` | millisekunder | Tid som tillbringats i frågekörning | 
| `indexLookupTimeInMs` | millisekunder | Tid i fysiskt indexlager | 
| `documentLoadTimeInMs` | millisekunder | Tid i inläsning av dokument  | 
| `systemFunctionExecuteTimeInMs` | millisekunder | Total tid för att utföra systemfunktioner (inbyggda) i millisekunder  | 
| `userFunctionExecuteTimeInMs` | millisekunder | Total tid som spenderas på att köra användardefinierade funktioner i millisekunder | 
| `retrievedDocumentCount` | count | Totalt antal hämtade dokument  | 
| `retrievedDocumentSize` | Byte | Total storlek på hämtade dokument i byte  | 
| `outputDocumentCount` | count | Antal utdatadokument | 
| `writeOutputTimeInMs` | millisekunder | Frågekörningstid i millisekunder | 
| `indexUtilizationRatio` | förhållande (<=1) | Förhållandet mellan antalet dokument som matchas av filtret och antalet inlästa dokument  | 

Klientens SDK:er kan internt göra flera frågeåtgärder för att hantera frågan inom varje partition. Klienten gör mer än ett anrop per `x-ms-max-item-count`partition om det totala resultatet överskrider , om frågan överskrider det etablerade dataflödet för partitionen, eller om frågenyttolasten når den maximala storleken per sida, eller om frågan når den systemallokerade timeout-gränsen. Varje partiell frågekörning `x-ms-documentdb-query-metrics` returnerar en för den sidan. 

Här är några exempelfrågor och hur du tolkar några av de mått som returneras från frågekörning: 

| Söka i data | Exempelmått | Beskrivning | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Antalet dokument som hämtas är 100+1 för att matcha TOP-satsen. Frågetid spenderas `WriteOutputTime` oftast `DocumentLoadTime` i och eftersom det är en genomsökning. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount är nu högre (500+1 för att matcha TOP-satsen). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Om 0,9 ms spenderas i IndexLookupTime för en nyckelsökning, eftersom `/N/?`det är en indexsökning på . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Något mer tid (1,7 ms) spenderas i IndexLookupTime över ett intervall `/N/?`scan, eftersom det är ett index sökning på . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Samma tid `DocumentLoadTime` som tidigare frågor, `WriteOutputTime` men lägre eftersom vi bara projicerar en egenskap. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Cirka 213 ms `UserDefinedFunctionExecutionTime` spenderas i att köra `c.N`UDF på varje värde av . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Ca 0,6 ms `IndexLookupTime` spenderas på `/Name/?`. Merparten av frågekörningstiden (~7 ms) i `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Frågan utförs som en genomsökning `LOWER`eftersom den använder och 500 av 2491 hämtade dokument returneras. |


## <a name="next-steps"></a>Nästa steg
* Mer information om sql-frågeoperatorer och nyckelord som stöds finns i [SQL Query](sql-query-getting-started.md). 
* Mer information om begäranheter finns i [begäranheterna](request-units.md).
* Mer information om indexeringsprincipen finns i [indexeringsprincipen](index-policy.md) 


