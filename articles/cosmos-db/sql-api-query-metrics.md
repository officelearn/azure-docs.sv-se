---
title: SQL-fråga mått för Azure Cosmos DB SQL API
description: Läs mer om hur du instrumenterar och felsöka SQL-frågeprestanda för Azure Cosmos DB-begäranden.
keywords: SQL-syntax, sql-fråga, sql-frågor, frågespråk för json, databasbegrepp och sql-frågor, mängdfunktioner
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: sngun
ms.openlocfilehash: 02f5cf7159847d6f67ee3d8e92805f785a58e959
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53142550"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Justera prestanda för frågor med Azure Cosmos DB

Azure Cosmos DB tillhandahåller en [SQL-API för att fråga data](how-to-sql-query.md), utan att kräva schema eller sekundära index. Den här artikeln innehåller följande information för utvecklare:

* Utförlig information om hur Azure Cosmos DB SQL-frågekörning fungerar
* Information om frågan sidhuvuden för begäran och svar och alternativ för klient-SDK
* Tips och bästa praxis för prestanda för frågor
* Exempel på hur du använder SQL-körningen statistik för att felsöka frågeprestanda

## <a name="about-sql-query-execution"></a>Om SQL-frågekörning

I Azure Cosmos DB kan du lagra data i behållare som kan växa till någon [storlek eller begäran genomflödet](partition-data.md). Azure Cosmos DB skalas sömlöst data över fysiska partitioner under försättsbladen att hantera datatillväxt eller öka i etablerat dataflöde. Du kan utfärda SQL-frågor till en behållare med hjälp av REST API eller en av de stöds [SQL SDK: er](sql-api-sdk-dotnet.md).

En kort översikt över partitionering: du definiera en partitionsnyckel som ”city”, som avgör hur data delas över fysiska partitioner. Data som hör till en enda partitionsnyckel (till exempel ”stad” == ”Seattle”) som lagrats i en fysisk partition, men en enda fysisk partition har vanligtvis flera partitionsnycklar. När en partition når storage storlek, tjänsten sömlöst delar upp partitionen i två nya partitioner och dividerar Partitionsnyckeln jämnt över dessa partitioner. Eftersom partitioner är tillfälligt, använda API: erna en abstraktion av en ”partitionsnyckelintervall”, som anger intervall för partition viktiga hashvärden. 

När du skickar en fråga till Azure Cosmos DB utför SDK dessa logiska steg:

* Parsa SQL-fråga för att fastställa frågeplan för körning. 
* Om frågan innehåller ett filter mot Partitionsnyckeln, som `SELECT * FROM c WHERE c.city = "Seattle"`, dirigeras den till en enda partition. Om frågan inte har ett filter på Partitionsnyckeln och den körs i alla partitioner, och resultatet slås samman på klientsidan.
* Frågan köras inom varje partition i serien eller parallell, baserat på klientkonfigurationen för. Inom varje partition frågan kan göra något eller mer tur och RETUR beroende på komplexiteten fråga konfigurerats sidstorlek och etablerat dataflöde i samlingen. Varje körning returnerar antalet [programbegäran](request-units.md) förbrukas av frågekörning och eventuellt frågestatistik för körning. 
* SDK: N utför en sammanfattning av frågeresultatet över partitioner. Till exempel om frågan innefattar en ORDER BY över partitioner, är sedan resultaten från enskilda partitioner merge sorterade att returnera resultat i globalt sorterade ordning. Om frågan är en sammansättning som `COUNT`, antalen från enskilda partitioner läggs ihop för att skapa det totala antalet.

SDK: erna tillhandahåller olika alternativ för frågekörning. Till exempel i .NET dessa alternativ är tillgängliga i den `FeedOptions` klass. I följande tabell beskrivs dessa alternativ och hur de påverkar körningstid för frågan. 

| Alternativ | Beskrivning |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Måste anges till true för någon fråga som måste köras på mer än en partition. Det här är en explicit flagga så att du kan fatta medvetna prestanda kompromisser under tiden för produktutveckling. |
| `EnableScanInQuery` | Måste anges till true om du har valt att inte indexera, men vill ändå köra frågan via en genomsökning. Endast har gäller om indexering för den begärda filter-sökvägen inaktiverats. | 
| `MaxItemCount` | Det maximala antalet objekt som ska returneras per serveranrop till servern. Genom att ange-1, kan du låta servern hantera antal objekt. Eller så kan du sänka det här värdet att hämta endast ett litet antal objekt per serveranrop. 
| `MaxBufferedItemCount` | Detta är ett alternativ för klientsidan och används för att begränsa minnesförbrukningen så att när du utför flera partitioner ORDER BY. Ett högre värde bidrar till att minska svarstiden för sortering av flera olika partitioner. |
| `MaxDegreeOfParallelism` | Hämtar eller anger antalet samtidiga åtgärder som körs på klientsidan under parallell frågekörning i tjänsten Azure Cosmos DB-databas. Ett positivt egenskapsvärde begränsar antalet samtidiga åtgärder till set-värde. Om det är inställt på mindre än 0, avgör systemet automatiskt antalet samtidiga åtgärder som ska köras. |
| `PopulateQueryMetrics` | Möjliggör detaljerad loggning av statistik tid har använt i olika faser i körningen av frågan som kompileringstid, loop-tiden för index och dokument läsa in tid. Du kan dela utdata från frågestatistik med Azures Support för att diagnostisera prestandaproblem för frågan. |
| `RequestContinuation` | Du kan återuppta Frågekörningen genom att skicka in täckande fortsättningstoken som returneras av en fråga. Fortsättningstoken kapslar in alla tillstånd som krävs för frågekörning. |
| `ResponseContinuationTokenLimitInKb` | Du kan begränsa den maximala storleken för fortsättningstoken som returnerades av servern. Du kan behöva ange detta om programmet värden har en gräns på rubriken svarsstorlek. Ställa in det här kan öka övergripande varaktighet och antalet förbrukade ru för frågan.  |

Exempel: Anta en exempelfråga på partitionsnyckel som begärdes för en samling med `/city` som partitionen viktiga och har etablerats med 100 000 RU/s genomströmning. Du begär den här frågan med hjälp av `CreateDocumentQuery<T>` i .NET som liknar följande:

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

SDK-kodavsnitt som visas ovan, motsvarar följande REST API-begäran:

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

Varje sida för körning av fråga motsvarar ett REST-API `POST` med den `Accept: application/query+json` rubrik och SQL-fråga i brödtexten. Varje fråga gör ett eller flera förfrågningar till servern med den `x-ms-continuation` token som ett eko mellan klienten och servern att återuppta körningen. Konfigurationsalternativen i FeedOptions skickas till servern i form av begärandehuvuden. Till exempel `MaxItemCount` motsvarar `x-ms-max-item-count`. 

Begäran returnerar följande (trunkerat för läsbarhet) svar:

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

Följande: viktiga svarshuvuden som returneras av frågan

| Alternativ | Beskrivning |
| ------ | ----------- |
| `x-ms-item-count` | Antal objekt som returnerades i svaret. Detta beror på den angivna `x-ms-max-item-count`, antalet objekt som ryms i nyttolasten för Maximal svarsstorlek, dataflöde och fråga körningstid. |  
| `x-ms-continuation:` | Fortsättningstoken att återuppta körningen av frågan, om ytterligare resultat är tillgängliga. | 
| `x-ms-documentdb-query-metrics` | Frågestatistik för körningen. Det här är en avgränsad sträng som innehåller statistik över tid i olika faser i Frågekörningen. Returneras om `x-ms-documentdb-populatequerymetrics` är inställd på `True`. | 
| `x-ms-request-charge` | Antalet [programbegäran](request-units.md) används av frågan. | 

Mer information om REST API-begärandehuvuden och alternativ finns [förfrågan efter resurser med hjälp av REST-API](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Metodtips för prestanda för frågor
Följande är de vanligaste faktorer som påverkar prestanda för Azure Cosmos DB-frågor. Vi närmare titt på var och en av de här ämnena i den här artikeln.

| Faktor | Tips | 
| ------ | -----| 
| Etablerat dataflöde | Mät RU per fråga och se till att du har det etablerade dataflödet som krävs för dina frågor. | 
| Partitionering och partitionsnycklar | Prioriterar frågor med partitionsnyckelvärdet i filterinstruktionen för låg latens. |
| SDK och frågealternativ | Följ de rekommenderade säkerhetsmetoderna för SDK som direkt anslutning och finjustera körning av klientsidan frågealternativ. |
| Svarstid för nätverk | Kontot för nätverk overhead i mätning och använda flera API: er för att läsa från den närmaste regionen. |
| Indexeringspolicy | Kontrollera att du har den nödvändiga sökvägar/indexprincip för frågan. |
| Fråga körningsstatistik | Analysera de mått för körning av frågan för att identifiera potentiella omskrivningar av frågor och former.  |

### <a name="provisioned-throughput"></a>Etablerat dataflöde
I Cosmos DB kan skapa du behållare av data, var och en med reserverat dataflöde, uttryckt i begäran enheter (RU) per sekund. En läsning av ett 1 KB-dokument är 1 RU, och varje åtgärd (inklusive frågor) normaliseras till ett fast antal ru: er baserat på dess komplexitet. Till exempel om du har 1000 RU/s har etablerats för din behållare och du har en fråga av typen `SELECT * FROM c WHERE c.city = 'Seattle'` som förbrukar 5 ru: er och du kan utföra (1000 RU/s) / (5 RU/fråga) = 200 fråga/s sådana frågor per sekund. 

Om du skickar in mer än 200 hälsoförfrågningar/sek, startar tjänsten hastighetsbegränsande inkommande begäranden över 200/s. SDK: erna hanterar automatiskt det här fallet genom att utföra ett backoff/återförsök, därför märker du en högre svarstider för de här frågorna. Ökar det etablerade dataflödet till det obligatoriska värdet förbättrar din svarstid och dataflöde. 

Mer information om begäransenheter finns [programbegäran](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partitionering och partitionsnycklar
Med Azure Cosmos DB, vanligtvis utför frågor i följande ordning från snabbaste/mest effektivt för att långsammare/mindre effektiv. 

* HÄMTA på en enda partitionsnyckel och Objektnyckel
* Fråga med en filtersats på en enda partitionsnyckel
* Fråga utan en filtersats för likhet eller ett intervall om en egenskap
* Skicka frågor utan filter

Frågor som behöver läsa alla partitioner måste svarstider och kan använda högre ru: er. Eftersom varje partition har automatisk indexering mot alla egenskaper, kan frågan hanteras effektivt från indexet i det här fallet. Du kan göra frågor som sträcker sig över partitioner snabbare genom att använda parallellitet.

Mer information om partitionering och partitionsnycklar finns [partitionering i Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK och frågealternativ
Se [prestandatips](performance-tips.md) och [prestandatestning](performance-testing.md) för hur du får bästa möjliga prestanda för klientsidan av Azure Cosmos DB. Detta inkluderar med de senaste SDK: er, konfigurera plattformsspecifika nätverkskonfigurationer som standardvärdet för antal anslutningar, frekvensen för skräpinsamling, samt med hjälp av enkel anslutningsalternativ som Direct/TCP. 


#### <a name="max-item-count"></a>Max antal objekt
För frågor, värdet för `MaxItemCount` kan ha en betydande inverkan på frågetiden för slutpunkt till slutpunkt. Varje tur och RETUR till servern kommer att returnera högst antal objekt i `MaxItemCount` (standard är 100 objekt). Du anger detta till ett högre värde (-1 är högsta och rekommenderade) förbättrar din övergripande frågevaraktigheten genom att begränsa antalet överföringar mellan servern och klienten, särskilt för frågor med stora resultatuppsättningar.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maxgrad av parallellitet
Frågor, finjustera det `MaxDegreeOfParallelism` att identifiera de bästa konfigurationerna för ditt program, särskilt om du utför flera partitioner frågor (utan ett filter på Partitionsnyckeln värdet). `MaxDegreeOfParallelism`  styr det maximala antalet parallella aktiviteter, t.ex, maximalt antalet partitioner besöks parallellt. 

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
* D = standard högsta antalet parallella aktiviteter (= totalt antal processorer i klientdatorn)
* P = användardefinierade maximala antalet parallella uppgifter
* N = antalet partitioner som behöver besökas för besvarande av en fråga

Följande är effekterna av hur de parallella frågorna skulle fungera för olika värden på s.
* (P == 0) = > seriell läge
* (P == 1) = > högst en aktivitet
* (P > 1) = > Min (P, N) parallella uppgifter 
* (P < 1) = > Min (N, D) parallella uppgifter

För SDK viktig information och information om implementerad klasser och metoder i [SQL SDK: er](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Svarstid för nätverk
Se [global distribution i Azure Cosmos DB](tutorial-global-distribution-sql-api.md) att konfigurera global distribution och ansluta till den närmaste regionen. Svarstid för nätverk har en betydande inverkan på prestanda för frågor när du behöver göra flera turer eller hämta en stor resultatmängd från frågan. 

I avsnittet om frågan körningsstatistik förklarar hur du hämtar servertid för körning av frågor ( `totalExecutionTimeInMs`), så att du kan skilja mellan tid i frågekörning och tid i nätverket överföring.

### <a name="indexing-policy"></a>Indexeringspolicy
Se [konfigurera indexeringsprincip](index-policy.md) för indexering sökvägar, typer, och lägen och hur de påverkar Frågekörningen. Som standard indexprincip använder hash-indexering för strängar, vilket är effektiva för likhetsfrågor, men inte för intervallet frågor/order by-frågor. Om du behöver omfångsfrågor för strängar, rekommenderar vi att ange intervallet Indextypen för alla strängar. 

## <a name="query-execution-metrics"></a>Fråga körningsstatistik
Du kan få detaljerad statistik om frågekörning genom att skicka in den valfria `x-ms-documentdb-populatequerymetrics` rubrik (`FeedOptions.PopulateQueryMetrics` i .NET SDK). Det värde som returneras i `x-ms-documentdb-query-metrics` har följande nyckel / värde-par som avsett för avancerad felsökning av Frågekörningen. 

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
| `totalExecutionTimeInMs` | millisekunder | Körningstid för fråga | 
| `queryCompileTimeInMs` | millisekunder | Fråga kompilering  | 
| `queryLogicalPlanBuildTimeInMs` | millisekunder | Dags att skapa logiska frågeplan | 
| `queryPhysicalPlanBuildTimeInMs` | millisekunder | Dags att skapa fysiska frågeplan | 
| `queryOptimizationTimeInMs` | millisekunder | Tid i Optimera fråga | 
| `VMExecutionTimeInMs` | millisekunder | Tid i frågan runtime | 
| `indexLookupTimeInMs` | millisekunder | Tid i fysiska index lager | 
| `documentLoadTimeInMs` | millisekunder | Tid i inläsning av dokument  | 
| `systemFunctionExecuteTimeInMs` | millisekunder | Total tid på verkställande system (inbyggda) funktioner i millisekunder  | 
| `userFunctionExecuteTimeInMs` | millisekunder | Total tid på köra användardefinierade funktioner i millisekunder | 
| `retrievedDocumentCount` | count | Totalt antal hämtade dokument  | 
| `retrievedDocumentSize` | byte | Total storlek på hämtade dokument i byte  | 
| `outputDocumentCount` | count | Antal dokument som utdata | 
| `writeOutputTimeInMs` | millisekunder | Tid i millisekunder för att köra frågan | 
| `indexUtilizationRatio` | förhållandet mellan (< = 1) | Förhållandet mellan antalet dokument som matchas av filtret till antalet dokument som läses in  | 

Klient-SDK: er göra internt flera frågeåtgärder att betjäna frågan inom varje partition. Klienten skickar mer än ett samtal per partition om det sammanlagda resultatet överskrider `x-ms-max-item-count`om frågan överstiger det etablerade dataflödet för partition, eller om frågan nyttolasten når maximal storlek per sida eller om frågan når systemet allokeras Timeout-gränsen. Varje partiella Frågekörningen returnerar en `x-ms-documentdb-query-metrics` för sidan. 

Här är några exempelfrågor och så här tolkar du några av mätvärdena som returneras från Frågekörningen: 

| Söka i data | Exempel-mått | Beskrivning | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Antal dokument som hämtats är 100 + 1 för att matcha TOP-satsen. Fråga tid det tar huvudsakligen i `WriteOutputTime` och `DocumentLoadTime` eftersom det är en sökning. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount är nu högre (500 + 1 för att matcha TOP-satsen). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Om 0,9 ms går åt i IndexLookupTime för nyckelsökningen, eftersom det är en sökning med indexet `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Lite mer tid (1.7 ms) på i IndexLookupTime över ett intervall skanna eftersom det är en sökning med indexet `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Samma tid som ägnats åt `DocumentLoadTime` som tidigare frågor, men lägre `WriteOutputTime` eftersom vi projicerar bara en egenskap. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Om 213 ms går åt i `UserDefinedFunctionExecutionTime` köra en användardefinierad funktion på varje värde i `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Om 0,6 ms går åt i `IndexLookupTime` på `/Name/?`. De flesta fråga körningstid (ms ~ 7) i `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Frågan utförs som en genomsökning eftersom den använder `LOWER`, och 500 utanför 2491 hämtade dokument returneras. |


## <a name="next-steps"></a>Nästa steg
* Läs om stöds SQL frågeoperatorer och nyckelorden i [SQL-fråga](how-to-sql-query.md). 
* Läs om begäransenheter i [programbegäran](request-units.md).
* Läs om indexeringsprincip i [indexeringsprincip](index-policy.md) 


