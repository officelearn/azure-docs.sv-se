---
title: "SQL-frågan mätvärden för Azure SQL DB-API Cosmos | Microsoft Docs"
description: "Lär dig mer om hur du instrumentera och felsöka frågeprestanda SQL Azure DB som Cosmos-begäranden."
keywords: "SQL-syntax, sql-fråga, sql-frågor, json-frågespråket, databasbegrepp och sql-frågor, mängdfunktioner"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: b2fa8e8f-7291-45a3-9bd1-7284ed9077f8
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: arramac
ms.openlocfilehash: 2cb6319356a536aebc1db3122cf80b8736d1fd4f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Justera prestanda för frågor med Azure Cosmos DB

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos-DB tillhandahåller en [SQL API för datafrågor](documentdb-sql-query.md), utan att schemat eller sekundärindex. Den här artikeln innehåller följande information för utvecklare:

* Utförlig information om hur fungerar Azure Cosmos DB SQL-frågan
* Information om frågan begärande- och svarshuvuden och alternativ för klient-SDK
* Tips och bästa praxis för prestanda för frågor
* Exempel på hur du använder SQL-körningen statistik för att felsöka prestanda för frågor

## <a name="about-sql-query-execution"></a>Om körning av SQL-fråga

I Azure Cosmos DB du lagra data i behållare, som kan växa till någon [storlek eller begäran genomflödet](partition-data.md). Azure Cosmos-DB skalas sömlöst data över fysiska partitioner under försättsbladen att hantera datatillväxt eller ökar med etablerat dataflöde. Du kan utfärda SQL-frågor till en behållare med REST API eller något av de stöds [SQL SDK](documentdb-sdk-dotnet.md).

En kort översikt över partitionering: du anger en partitionsnyckel som ”stad”, som avgör hur data delas mellan fysiska partitioner. Data som hör till en enda partitionsnyckel (till exempel ”stad” == ”Seattle”) lagras i en fysiska partition, men en enda fysisk partition har vanligtvis flera partitionsnycklar. När en partition når lagringsstorlek, tjänsten sömlöst delar partitionen i två nya partitioner och dividerar Partitionsnyckeln jämnt mellan dessa partitioner. Eftersom partitioner är tillfälligt använda API: er i en abstraktion av en ”partitionsnyckelintervallet”, som anger intervall för partition viktiga hashvärden. 

När du skickar en fråga till Azure Cosmos DB utför stegen logiska SDK:

* Parsa SQL-frågan för att fastställa körning frågeplanen. 
* Om frågan innehåller ett filter mot Partitionsnyckeln, som `SELECT * FROM c WHERE c.city = "Seattle"`, dirigeras till en enskild partition. Om frågan inte har ett filter på partitionsnyckel, sedan den körs i alla partitioner och resultat kombineras på klientsidan.
* Frågan köras inom varje partition i serien eller parallell, baserat på klientens konfiguration. Inom varje partition frågan kan göra att en eller flera sändningar beroende på hur komplex fråga konfigurerats sidstorlek och etableras genomflöde i mängden. Varje körning returnerar antalet [programbegäran](request-units.md) används genom att köra frågor och eventuellt statistik för körning av frågan. 
* SDK utför en sammanfattning av frågeresultatet över partitioner. Till exempel om frågan innefattar en ORDER BY över partitioner, är sedan resultaten från enskilda partitioner merge sorteras du returnerar resultat på globalt sorteringsordningen. Om frågan är en samling som `COUNT`, antal från enskilda partitioner summeras för att skapa det totala antalet.

SDK: erna tillhandahåller olika alternativ för frågekörning. Till exempel i .NET dessa alternativ är tillgängliga i den `FeedOptions` klass. I följande tabell beskrivs dessa alternativ och hur de påverkar körningstid för frågan. 

| Alternativ | Beskrivning |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Måste anges till true för en fråga som måste köras på mer än en partition. Det här är en explicit flagga så att du kompromissa medvetna prestanda under utveckling. |
| `EnableScanInQuery` | Måste anges till true om du har valt att inte indexera, men vill ändå kör frågan via en genomsökning. Endast är tillgängligt för indexering för den begärda filtret sökvägen inaktiverad. | 
| `MaxItemCount` | Maximalt antal objekt som ska returneras per tur och RETUR till servern. Genom att ange-1, kan du låta servern hantera antalet objekt. Eller så kan du sänka detta värde att hämta litet antal objekt per onödig kommunikation. 
| `MaxBufferedItemCount` | Detta är ett alternativ för klientsidan och används för att begränsa minnesförbrukningen när du utför cross-partition ORDER BY. Ett högre värde hjälper till att minska svarstiden för cross-partition sortering. |
| `MaxDegreeOfParallelism` | Hämtar eller anger antalet samtidiga åtgärder som körs på klientsidan under parallell frågekörning i tjänsten Azure DB som Cosmos-databasen. Ett positivt egenskapsvärde begränsar antalet samtidiga åtgärder att ange värdet. Om det är mindre än 0, bestämmer systemet automatiskt antalet samtidiga åtgärder att köra. |
| `PopulateQueryMetrics` | Aktiverar detaljerad loggning av statistik tid som ägnats åt olika faser i Frågekörningen som kompileringstid, indexet loop tid och dokument laddas gång. Utdata från frågan statistik kan du dela med Azure-supporten att diagnostisera prestandaproblem för frågan. |
| `RequestContinuation` | Du kan återuppta körning av fråga genom att passera i täckande fortsättningstoken som returnerades av en fråga. Fortsättningstoken kapslar in alla tillstånd som krävs för frågekörning. |
| `ResponseContinuationTokenLimitInKb` | Du kan begränsa den maximala storleken för fortsättningstoken som returnerades av servern. Du kan behöva ange detta om din programvärden har gränser för huvudstorlek. Anger det här kan öka den övergripande varaktighet och RUs som används för frågan.  |

Till exempel ta en exempelfråga på partitionsnyckel som efterfrågas på en samling med `/city` som partition nyckel och etablerats med 100 000 RU/s genomströmning. Du begär den här frågan med `CreateDocumentQuery<T>` i .NET på följande:

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

SDK-fragment ovan motsvarar följande REST API-begäran:

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

Varje sida för körning av frågan som motsvarar en REST-API `POST` med den `Accept: application/query+json` sidhuvud och SQL-frågan i brödtexten. Varje fråga gör en eller flera förfrågningar till servern med den `x-ms-continuation` token eko mellan klienten och servern för att återuppta körning. Konfigurationsalternativen i FeedOptions skickas till servern i form av huvuden för begäran. Till exempel `MaxItemCount` motsvarar `x-ms-max-item-count`. 

Begäran returnerar följande (trunkerad för läsbarhet) svar:

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

Nyckel-svarshuvuden som returnerades från frågan inkluderar följande:

| Alternativ | Beskrivning |
| ------ | ----------- |
| `x-ms-item-count` | Antal objekt som returneras i svaret. Detta är beroende av den angivna `x-ms-max-item-count`, antalet objekt som får plats i nyttolasten för Maximal svarsstorlek, dataflöde och körningstid för frågan. |  
| `x-ms-continuation:` | Fortsättningstoken att återuppta körning av frågan, om ytterligare resultat är tillgängliga. | 
| `x-ms-documentdb-query-metrics` | Frågan statistik för körning. Det här är en avgränsad sträng som innehåller statistik över tid i olika faser i frågan. Returneras om `x-ms-documentdb-populatequerymetrics` är inställd på `True`. | 
| `x-ms-request-charge` | Antalet [programbegäran](request-units.md) förbrukas av frågan. | 

Mer information om REST API-huvuden för begäran och alternativ finns [förfrågan efter resurser med hjälp av REST-API](https://docs.microsoft.com/rest/api/documentdb/querying-documentdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Bästa praxis för prestanda för frågor
Följande är de vanligaste faktorer som påverkar Azure Cosmos DB frågeprestanda. Vi gräva djupare i var och en av dessa avsnitt i den här artikeln.

| Faktor | Tips | 
| ------ | -----| 
| Etablerat dataflöde | Mät RU per fråga och se till att du har det tillhandahållna dataflödet som krävs för dina frågor. | 
| Partitionering och partitionsnycklar | Ge företräde åt frågor med partitionsnyckelvärde i filterinstruktionen för låg fördröjning. |
| SDK-och frågealternativ | Följ Metodtips för SDK som direkt anslutning och finjustera frågealternativ körning på klientsidan. |
| Svarstid för nätverk | Kontot för nätverk omkostnader i mått och använda flera API: er för att läsa från den närmaste regionen. |
| Indexprincip | Se till att du har den nödvändiga sökvägar/indexprincip för frågan. |
| Mätvärden för körning av frågan | Analysera de mått för körning av frågan för att identifiera potentiella omskrivningar av frågor och former.  |

### <a name="provisioned-throughput"></a>Etablerat dataflöde
Skapa behållare av data med reserverat dataflöde, uttryckt i begäran enheter (RU) per sekund i Cosmos DB. En läsning av ett dokument på 1 KB är 1 RU och varje åtgärd (inklusive frågor) är normaliserat till ett fast antal RUs baserat på dess komplexitet. Till exempel om du har 1000 RU/s som skapats för din behållare och du har en fråga som `SELECT * FROM c WHERE c.city = 'Seattle'` som förbrukar 5 RUs och du kan utföra (1000 RU/s) / (5 RU/query) = 200 frågan/s sådana frågor per sekund. 

Om du skickar fler än 200 per sekund startas tjänsten hastighetsbegränsande inkommande begäranden över 200/s. SDK: erna hanterar automatiskt det här fallet genom att utföra en backoff/försök, därför ser du kanske en högre latens för dessa frågor. Öka dataflöde till det obligatoriska värdet förbättrar dina svarstid och genomströmning. 

Mer information om frågeenheter finns [programbegäran](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partitionering och partitionsnycklar
Med Azure Cosmos DB brukar utföra frågor i följande ordning från snabbaste/mest effektiva till långsammare/mindre effektiva. 

* HÄMTA på en enda partitionsnyckel och objektet nyckel
* Frågan med en filtersats på en enda partitionsnyckel
* Fråga utan en likhet eller intervallet filtersats om en egenskap
* Fråga utan filter

Frågor som behöver kontakta alla partitioner måste högre latens och kan använda högre RUs. Eftersom varje partition har automatisk indexering mot alla egenskaper, kan frågan hanteras effektivt från indexet i det här fallet. Du kan skapa frågor som sträcker sig över partitioner snabbare genom att använda parallellitet.

Mer information om partitionering och partitionsnycklar finns [partitionering i Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK-och frågealternativ
Se [prestandatips](performance-tips.md) och [prestandatester](performance-testing.md) att få bästa möjliga prestanda för klientsidan från Azure Cosmos DB. Detta inkluderar med de senaste SDK: er, konfigurera plattformsspecifika konfigurationer som Standardantal anslutningar, frekvensen för skräpinsamling, samt med lightweight anslutningsalternativ som direkt/TCP. 


#### <a name="max-item-count"></a>Max antal objekt
För frågor med värdet för `MaxItemCount` kan ha en betydande inverkan på Frågetid för slutpunkt till slutpunkt. Varje onödig kommunikation till servern returnerar inga fler än antalet objekt i `MaxItemCount` (standard 100 objekt). Ange detta till ett högre värde (-1 är högsta och rekommenderade) förbättrar din övergripande varaktighet för frågan genom att begränsa antalet sändningar mellan servern och klienten, särskilt för frågor med stora resultatuppsättningar.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Max grad av parallellitet
Frågor, finjustera den `MaxDegreeOfParallelism` att identifiera de bästa konfigurationerna för programmet, särskilt om du utför mellan partition-frågor (utan ett filter på partitionsnyckel värdet). `MaxDegreeOfParallelism`Anger det maximala antalet parallella aktiviteter, t.ex. maximalt antalet partitioner som ska användas parallellt. 

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

Vi antar som
* D = standard maximalt antal parallella aktiviteter (= totalt antal processorn i klientdatorn)
* P = användaren har angett maximalt antal parallella aktiviteter
* N = antalet partitioner som ska användas för att svara på en fråga

Följande är effekterna av hur de parallella frågorna skulle fungera för olika värden för P.
* (P == 0) = > Serial-läge
* (P == 1) = > maximalt en uppgift
* (P > 1) = > Min (P, N) parallella aktiviteter 
* (P < 1) = > Min (N, D) parallella aktiviteter

För SDK viktig information och information om implementerade klasser och metoder finns [SQL-SDK](documentdb-sdk-dotnet.md)

### <a name="network-latency"></a>Svarstid för nätverk
Se [Azure Cosmos DB global distributionsplatsen](tutorial-global-distribution-documentdb.md) att konfigurera distributionslistor och ansluta till den närmaste regionen. Nätverksfördröjningen har en betydande inverkan på prestanda när du behöver göra flera turer eller hämta en stor resultatmängd från frågan. 

Avsnitt om frågan körning mått förklarar hur du hämta server körningstiden för frågor ( `totalExecutionTimeInMs`), så att du kan skilja mellan tid i Frågekörningen och tid i nätverket överföring.

### <a name="indexing-policy"></a>Indexprincip
Se [konfigurera indexprincip](indexing-policies.md) för indexering sökvägar, typer, och lägen och hur de påverkar Frågekörningen. Som standard indexprincip använder hash-indexering för strängar, vilket är effektiv för likhetsfrågor, men inte för intervall frågor/order by-frågor. Om du behöver intervallet frågor efter strängar, rekommenderar vi att ange intervallet index för alla strängar. 

## <a name="query-execution-metrics"></a>Mätvärden för körning av frågan
Du kan få detaljerad mått på körning av fråga om den valfria `x-ms-documentdb-populatequerymetrics` huvud (`FeedOptions.PopulateQueryMetrics` i .NET SDK). Det värde som returneras i `x-ms-documentdb-query-metrics` har följande nyckel-värdepar avsett för avancerad felsökning av Frågekörningen. 

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
| `totalExecutionTimeInMs` | millisekunder | Körningstid för frågan | 
| `queryCompileTimeInMs` | millisekunder | Frågan kompilering  | 
| `queryLogicalPlanBuildTimeInMs` | millisekunder | Tid att skapa logiska frågeplan | 
| `queryPhysicalPlanBuildTimeInMs` | millisekunder | Tid att skapa fysisk frågeplan | 
| `queryOptimizationTimeInMs` | millisekunder | Tid som ägnats åt att optimera frågan | 
| `VMExecutionTimeInMs` | millisekunder | Tid i frågan runtime | 
| `indexLookupTimeInMs` | millisekunder | Tid i fysiska index lager | 
| `documentLoadTimeInMs` | millisekunder | Tid i inläsning av dokument  | 
| `systemFunctionExecuteTimeInMs` | millisekunder | Total tid som ägnats verkställande system (inbyggda) funktioner i millisekunder  | 
| `userFunctionExecuteTimeInMs` | millisekunder | Total tid som ägnats verkställande användardefinierade funktioner i millisekunder | 
| `retrievedDocumentCount` | antal | Totalt antal hämtade dokument  | 
| `retrievedDocumentSize` | byte | Total storlek på hämtade dokument i byte  | 
| `outputDocumentCount` | antal | Antal dokument som utdata | 
| `writeOutputTimeInMs` | millisekunder | Tid i millisekunder för att köra frågan | 
| `indexUtilizationRatio` | förhållandet mellan (< = 1) | Förhållandet mellan antalet dokument som matchar filtret till antal dokument som läses in  | 

Klient-SDK: internt göra flera frågeåtgärder att utföra frågan inom varje partition. Klienten gör flera anrop per partition om totalt resultat överskrider `x-ms-max-item-count`, om frågan överskrider det tillhandahållna dataflödet för partition, eller om frågan nyttolasten når maximal storlek per sida, eller om frågan når systemets allokerade Timeout-gränsen. Varje partiella Frågekörningen returnerar en `x-ms-documentdb-query-metrics` för sidan. 

Här är några exempelfrågor och hur du tolkar vissa av mätvärdena som returnerades från Frågekörningen: 

| Fråga | Exempel mått | Beskrivning | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Antal dokument som hämtats är 100 + 1 för att matcha TOP-instruktion. Frågetid används främst i `WriteOutputTime` och `DocumentLoadTime` eftersom det är en genomsökning. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount är nu högre (500 + 1 för att matcha TOP-instruktion). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Om 0,9 ms används i IndexLookupTime för en nyckel sökning, eftersom det är ett index sökning `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Något mer tid (1,7 ms) som används IndexLookupTime via en genomsökning för intervallet, eftersom det är ett index sökning `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Samma tid som ägnats `DocumentLoadTime` som tidigare frågor men lägre `WriteOutputTime` eftersom vi projicerar bara en egenskap. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Om 213 ms ägnats åt `UserDefinedFunctionExecutionTime` köra en användardefinierad funktion på varje värde i `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Om 0,6 ms ägnats åt `IndexLookupTime` på `/Name/?`. De flesta av frågan körningstid (ms ~ 7) i `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Frågan utförs som en genomsökning eftersom den använder `LOWER`, och 500 utanför 2491 hämtade dokument som returneras. |


## <a name="next-steps"></a>Nästa steg
* Läs om stöds SQL frågeoperatorer och nyckelord i [SQL-frågan](documentdb-sql-query.md). 
* Mer information om frågeenheter, se [programbegäran](request-units.md).
* Läs om indexprincip i [indexering princip](indexing-policies.md) 


