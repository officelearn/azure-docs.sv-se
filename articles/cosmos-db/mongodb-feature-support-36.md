---
title: Azure Cosmos DB:s API för MongoDB (3.6-version) stöds funktioner och syntax
description: Lär dig mer om Azure Cosmos DB:s API för MongoDB (3.6 version) som stöds funktioner och syntax.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5df21b2c1926803a65eca911c66b059f36ee18aa
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393618"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB:s API för MongoDB (version 3.6): Funktioner och syntax som stöds

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan kommunicera med Azure Cosmos DB:s API för MongoDB med någon av de drivrutiner för MongoDB med öppen källkod [.](https://docs.mongodb.org/ecosystem/drivers) Azure Cosmos DB:s API för MongoDB tillåter gör det möjligt att använda befintliga klientdrivrutiner genom att följa MongoDB-[trådprotokollet](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Genom att använda Azure Cosmos DB:s API för MongoDB kan du dra nytta av fördelarna med den MongoDB du är van vid, med alla företagsfunktioner som Cosmos DB tillhandahåller: [global distribution,](distribute-data-globally.md) [automatisk sharding,](partition-data.md)tillgänglighet och svarstidsgarantier, kryptering i vila, säkerhetskopior och mycket mer.

## <a name="protocol-support"></a>Protokollstöd

Azure Cosmos DB:s API för MongoDB är kompatibelt med MongoDB-serverversion **3.6** som standard för nya konton. De operatorer som stöds och eventuella begränsningar eller undantag anges nedan. Klientdrivrutiner som förstår dessa protokoll bör kunna ansluta till Cosmos DB:s API för MongoDB. Observera att när du använder Azure Cosmos DB:s API för MongoDB-konton har 3.6-versionen av konton slutpunkten i formatet `*.mongo.cosmos.azure.com` `*.documents.azure.com`medan 3.2-versionen av konton har slutpunkten i formatet .

## <a name="query-language-support"></a>Stöd för frågespråk

Azure Cosmos DB:s API för MongoDB erbjuder omfattande stöd för MongoDB-frågespråkskonstruktioner. Nedan hittar du en detaljerad lista över åtgärder som stöds, operatorer, faser, kommandon och alternativ.

## <a name="database-commands"></a>Databaskommandon

Azure Cosmos DB:s API för MongoDB stöder följande databaskommandon:

### <a name="query-and-write-operation-commands"></a>Fråga och skriv-åtgärdskommandon

|Kommando  |Stöds |
|---------|---------|
|delete | Ja |
|find | Ja     |
|findAndModify | Ja  |
|getLastError|   Ja |
|getMore  |  Ja  |
|fåPrevError | Inga  |
|insert  |   Ja  |
|parallelCollectionScan  | Ja   |
|resetError |    Inga  |
|update  |   Ja  |
|[Ändra dataströmmar](mongodb-change-streams.md)  |  Ja  |
|GridFS (GridFS) |   Ja  |

### <a name="authentication-commands"></a>Autentiseringskommandon

|Kommando  |Stöds |
|---------|---------|
|authenticate    |   Ja      |
|logout    |      Ja   |
|getnonce   |    Ja     |


### <a name="administration-commands"></a>Administrationskommandon

|Kommando  |Stöds |
|---------|---------|
|Begränsade samlingar   |   Inga      |
|klonCollectionAsCapped     |   Inga      |
|collMod (collMod)     |   Inga      |
|collMod: expireAfterSeconds   |   Inga      |
|konverteraToCapped   |  Inga       |
|copydb     |  Inga       |
|skapa   |    Ja     |
|createIndexes     |  Ja       |
|currentOp (currentOp)     |  Ja       |
|drop     |   Ja      |
|dropDatabase     |  Ja       |
|dropIndexes     |   Ja      |
|filemd5    |   Ja      |
|killCursors    |  Ja       |
|killOp (på)     |   Inga      |
|listCollections     |  Ja       |
|listDatabases     |  Ja       |
|listIndexes     |  Ja       |
|reIndex     |    Ja     |
|byta namn På NamnInktion     |    Inga     |
|connectionStatus    |     Inga    |

### <a name="diagnostics-commands"></a>Diagnostiska kommandon

|Kommando  |Stöds |
|---------|---------|
|buildInfo         |   Ja      |
|collStats    |  Ja       |
|connPoolStats (på)     |  Inga       |
|connectionStatus     |  Inga       |
|dataStorlek     |   Inga      |
|dbHash (på ett sätt)    |    Inga     |
|dbStats     |   Ja      |
|Förklara     |   Ja      |
|förklara: executionStats     |   Ja      |
|funktioner     |    Inga     |
|hostInfo     |   Inga      |
|listDatabases         |   Ja      |
|listaKommands     |  Inga       |
|profilerar     |  Inga       |
|serverStatus     |  Inga       |
|överst     |    Inga     |
|whatsmyuri     |   Ja      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Sammansättningspipeline</a>

### <a name="aggregation-commands"></a>Sammansättningskommandon

|Kommando  |Stöds |
|---------|---------|
|aggregate |   Ja  |
|count     |   Ja  |
|distinct  | Ja |
|kartaReeduce | Inga |

### <a name="aggregation-stages"></a>Sammansättningsfaser

|Kommando  |Stöds |
|---------|---------|
|$collStats    |Inga|
|$project    |Ja|
|$match    |Ja|
|$redact|    Ja|
|$limit    |Ja|
|$skip    |Ja|
|$unwind|    Ja|
|$group    |    Ja|
|$sample|        Ja|
|$sort    |Ja|
|$geoNear|    Inga|
|$lookup    |    Ja|
|$out        |Ja|
|$indexStats|        Inga|
|$facet    |Inga|
|$bucket|    Inga|
|$bucketAuto|    Inga|
|$sortByCount|    Ja|
|$addFields    |Ja|
|$replaceRoot|    Ja|
|$count    |Ja|
|$currentOp|    Inga|
|$listLocalSessions    |Inga|
|$listSessions    |Inga|
|$graphLookup    |Inga|

### <a name="boolean-expressions"></a>Booleska uttryck

|Kommando  |Stöds |
|---------|---------|
|$and| Ja|
|$or|Ja|
|$not|Ja|

### <a name="set-expressions"></a>Uttryck för angivelse

|Kommando  |Stöds |
|---------|---------|
| $setEquals | Ja|
|$setIntersection|Ja|
| $setUnion|Ja|
| $setDifference|Ja|
| $setIsSubset|Ja|
| $anyElementTrue|Ja|
| $allElementsTrue|Ja|

### <a name="comparison-expressions"></a>Jämförelseuttryck

|Kommando  |Stöds |
|---------|---------|
|$cmp     |  Ja       |
|$eq|    Ja| 
|$gt |    Ja| 
|$gte|    Ja| 
|$lt    |Ja|
|$lte|    Ja| 
|$ne    |    Ja| 
|$in    |    Ja| 
|$nin    |    Ja| 

### <a name="arithmetic-expressions"></a>Aritmetiska uttryck

|Kommando  |Stöds |
|---------|---------|
|$abs |  Ja       |
| $add |  Ja       |
| $ceil |  Ja       |
| $divide |  Ja       |
| $exp |  Ja       |
| $floor |  Ja       |
| $ln |  Ja       |
| $log |  Ja       |
| $log10 |  Ja       |
| $mod |  Ja       |
| $multiply |  Ja       |
| $pow |  Ja       |
| $sqrt |  Ja       |
| $subtract |  Ja       |
| $trunc |  Ja       |

### <a name="string-expressions"></a>Stränguttryck

|Kommando  |Stöds |
|---------|---------|
|$concat |  Ja       |
| $indexOfBytes|  Ja       |
| $indexOfCP|  Ja       |
| $split|  Ja       |
| $strLenBytes|  Ja       |
| $strLenCP|  Ja       |
| $strcasecmp|  Ja       |
| $substr|  Ja       |
| $substrBytes|  Ja       |
| $substrCP|  Ja       |
| $toLower|  Ja       |
| $toUpper|  Ja       |

### <a name="text-search-operator"></a>Operator för textsökning

|Kommando  |Stöds |
|---------|---------|
| $meta | Inga|

### <a name="array-expressions"></a>Matrisuttryck

|Kommando  |Stöds |
|---------|---------|
|$arrayElemAt    |    Ja|
|$arrayToObject|    Ja|
|$concatArrays    |    Ja|
|$filter    |    Ja|
|$indexOfArray    |Ja|
|$isArray    |    Ja|
|$objectToArray    |Ja|
|$range    |Ja|
|$reverseArray    |    Ja|
|$reduce|    Ja|
|$size    |    Ja|
|$slice    |    Ja|
|$zip    |    Ja|
|$in    |    Ja|

### <a name="variable-operators"></a>Variabla operatorer

|Kommando  |Stöds |
|---------|---------|
|$map    |Inga|
|$let    |Ja|

### <a name="system-variables"></a>Systemvariabler

|Kommando  |Stöds |
|---------|---------|
|$$CURRENT|    Ja|
|$$DESCEND|        Ja|
|$$KEEP        |Ja|
|$$PRUNE    |    Ja|
|$$REMOVE    |Ja|
|$$ROOT        |Ja|

### <a name="literal-operator"></a>Bokstavlig operator

|Kommando  |Stöds |
|---------|---------|
|$literal    |Ja|

### <a name="date-expressions"></a>Datumuttryck

|Kommando  |Stöds |
|---------|---------|
|$dayOfYear    |Ja    |
|$dayOfMonth|    Ja    |
|$dayOfWeek    |Ja    |
|$year    |Ja    |
|$month    |Ja|    
|$week    |Ja    |
|$hour    |Ja    |
|$minute|    Ja|    
|$second    |Ja    |
|$millisecond|    Ja|    
|$dateToString    |Ja    |
|$isoDayOfWeek    |Ja    |
|$isoWeek    |Ja    |
|$dateFromParts|    Inga|    
|$dateToParts    |Inga    |
|$dateFromString|    Inga|
|$isoWeekYear    |Ja    |

### <a name="conditional-expressions"></a>Villkorliga uttryck

|Kommando  |Stöds |
|---------|---------|
| $cond| Ja|
| $ifNull| Ja|
| $switch |Ja|

### <a name="data-type-operator"></a>Operator för datatyp

|Kommando  |Stöds |
|---------|---------|
| $type| Ja|

### <a name="accumulator-expressions"></a>Flervalsuttryck

|Kommando  |Stöds |
|---------|---------|
|$sum    |Ja    |
|$avg    |Ja    |
|$first|    Ja|
|$last    |Ja    |
|$max    |Ja    |
|$min    |Ja    |
|$push|    Ja|
|$addToSet|    Ja|
|$stdDevPop|    Inga    |
|$stdDevSamp|    Inga|

### <a name="merge-operator"></a>Koppla operator

|Kommando  |Stöds |
|---------|---------|
| $mergeObjects | Ja|

## <a name="data-types"></a>Datatyper

|Kommando  |Stöds |
|---------|---------|
|Double    |Ja    |
|Sträng    |Ja    |
|Objekt    |Ja    |
|Matris    |Ja    |
|Binära data    |Ja|    
|ObjectId    |Ja    |
|Boolesk    |Ja    |
|Date    |Ja    |
|Null    |Ja    |
|32-bitars heltal (int)    |Ja    |
|Tidsstämpel    |Ja    |
|64-bitars heltal (lång)    |Ja    |
|MinKey (min nyckel)    |Ja    |
|MaxKey (MaxKey)    |Ja    |
|Decimal128    |Ja|    
|Reguljärt uttryck    |Ja|
|JavaScript    |Ja|
|JavaScript (med omfattning)|    Ja    |
|Undefined (Odefinierad)    |Ja    |

## <a name="indexes-and-index-properties"></a>Index och indexegenskaper

### <a name="indexes"></a>Index

|Kommando  |Stöds |
|---------|---------|
|Ett fältindex    |Ja    |
|Sammansatt index    |Ja    |
|Index för flera nycklar    |Ja    |
|Textindex    |Inga|
|2dsphere (2dsphere)    |Ja    |
|2d-index    |Inga    |
|Hashed Index    | Ja|

### <a name="index-properties"></a>Indexegenskaper

|Kommando  |Stöds |
|---------|---------|
|TTL|    Ja    |
|Unik    |Ja|
|Delvis|    Inga|
|Fall Okänslig    |Inga|
|Utspridda    |Inga |
|Bakgrund|    Ja |

## <a name="operators"></a>Operatorer

### <a name="logical-operators"></a>Logiska operatorer

|Kommando  |Stöds |
|---------|---------|
|$or    |    Ja|
|$and    |    Ja|
|$not    |    Ja|
|$nor    |    Ja| 

### <a name="element-operators"></a>Elementoperatorer

|Kommando  |Stöds |
|---------|---------|
|$exists|    Ja|
|$type    |    Ja|

### <a name="evaluation-query-operators"></a>Frågeoperatorer för utvärdering

|Kommando  |Stöds |
|---------|---------|
|$expr    |    Inga|
|$jsonSchema    |    Inga|
|$mod    |    Ja|
|$regex |    Ja|
|$text    | Nej (Stöds inte. Använd $regex i stället.)| 
|$where    |Inga| 

I $regex frågor tillåter vänsterförankrade uttryck indexsökning. Om du använder modifierarna i (skiftlägesokänsligt) och m (flera rader) så får du dock samlingsskanningen i alla uttryck.

När det finns ett behov att inkludera $ eller | så är det bäst att skapa två (eller flera) regex-frågor. Med tanke på följande ursprungliga ```find({x:{$regex: /^abc$/})```fråga: måste den ändras på följande sätt:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Den första delen använder indexet för att begränsa sökningen till dokument som börjar med ^abc och den andra delen matchar de exakta posterna. Fält-operatorn | fungerar som en or-funktion – frågan ```find({x:{$regex: /^abc|^def/})``` matchar dokumenten där fältet x har värden som börjar med abc eller def. Om du vill använda indexet så rekommenderar vi att du delar upp frågan i två olika frågor anslutna med operatorn $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Matrisoperatorer

|Kommando  |Stöds | 
|---------|---------|
| $all | Ja| 
| $elemMatch | Ja| 
| $size | Ja | 

### <a name="comment-operator"></a>Kommentar operatör

|Kommando  |Stöds | 
|---------|---------|
$comment |Ja| 

### <a name="projection-operators"></a>Projektionsoperatörer

|Kommando  |Stöds |
|---------|---------|
|$elemMatch    |Ja|
|$meta|    Inga|
|$slice    | Ja|

### <a name="update-operators"></a>Uppdateringsoperatorer

#### <a name="field-update-operators"></a>Fältuppdateringsoperatorer

|Kommando  |Stöds |
|---------|---------|
|$inc    |    Ja|
|$mul    |    Ja|
|$rename    |    Ja|
|$setOnInsert|    Ja|
|$set    |Ja|
|$unset| Ja|
|$min    |Ja|
|$max    |Ja|
|$currentDate    | Ja|

#### <a name="array-update-operators"></a>Matrisuppdateringsoperatorer

|Kommando  |Stöds |
|---------|---------|
|$    |Ja|
|$[]|    Ja|
|$[<identifier>]|    Ja|
|$addToSet    |Ja|
|$pop    |Ja|
|$pullAll|    Ja|
|$pull    |Ja|
|$push    |Ja|
|$pushAll| Ja|


#### <a name="update-modifiers"></a>Uppdatera modifierare

|Kommando  |Stöds |
|---------|---------|
|$each    |    Ja|
|$slice    |Ja|
|$sort    |Ja|
|$position    |Ja|

#### <a name="bitwise-update-operator"></a>Binär uppdateringsoperator

|Kommando  |Stöds |
|---------|---------|
| $bit    |    Ja|    
|$bitsAllSet    |    Inga|
|$bitsAnySet    |    Inga|
|$bitsAllClear    |Inga|
|$bitsAnyClear    |Inga|

### <a name="geospatial-operators"></a>Geospatiala operatorer

Operator | Stöds| 
--- | --- |
$geoWithin | Ja |
$geoIntersects | Ja | 
$near |  Ja |
$nearSphere |  Ja |
$geometry |  Ja |
$minDistance | Ja |
$maxDistance | Ja |
$center | Ja |
$centerSphere | Ja |
$box | Ja |
$polygon |  Ja |

## <a name="cursor-methods"></a>Markör-metoder

|Kommando  |Stöds |
|---------|---------|
|cursor.batchSize()    |    Ja|
|cursor.close()    |Ja|
|cursor.isClosed()|        Ja|
|cursor.collation()|    Inga|
|cursor.comment()    |Ja|
|cursor.count()    |Ja|
|cursor.explain()|    Inga|
|cursor.forEach()    |Ja|
|cursor.hasNästa()    |Ja|
|cursor.hint()    |Ja|
|cursor.isExhausted()|    Ja|
|cursor.itcount()    |Ja|
|cursor.limit()    |Ja|
|cursor.map()    |Ja|
|cursor.maxScan()    |Ja|
|cursor.maxTimeMS()|    Ja|
|cursor.max()    |Ja|
|cursor.min()    |Ja|
|cursor.next()|    Ja|
|cursor.noCursorTimeout()    |Inga|
|cursor.objsLeftInBatch()    |Ja|
|cursor.pretty()|    Ja|
|cursor.readConcern()|    Ja|
|cursor.readPref()        |Ja|
|cursor.returnKey()    |Inga|
|cursor.showRecordId()|    Inga|
|cursor.size()    |Ja|
|cursor.skip()    |Ja|
|cursor.sort()    |    Ja|
|cursor.tailable()|    Inga|
|cursor.toArray()    |Ja|

## <a name="sort-operations"></a>Sorteringsåtgärder

Med åtgärden `findOneAndUpdate` kan du använda sorteringsåtgärder på ett enda fält, men inte på flera fält.

## <a name="unique-indexes"></a>Unika index

Unika index säkerställer att ett visst fält inte har dubblettvärden för alla dokument i en samling, ungefär som det sätt på vilket unikhet bevaras på standardnyckeln "_id". Du kan skapa anpassade index i Cosmos DB med kommandot createIndex, inklusive det "unika" villkoret.

## <a name="time-to-live-ttl"></a>TTL-värde (time to live)

Cosmos DB stöder Time to Live (TTL) baserat på dokumentets tidsstämpel. Du kan aktivera TTL för samlingar genom att gå till [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Användar- och rollhantering

Cosmos DB stöder ännu inte användare och roller. Cosmos DB stöder dock rollbaserad åtkomstkontroll (RBAC) och läs- och skrivskyddade lösenord/nycklar som kan erhållas via [Azure-portalen](https://portal.azure.com) (sidan Anslutningssträng).

## <a name="replication"></a>Replikering

Cosmos DB stöder automatisk, inbyggd replikering på de understa lagren. Denna logik utökas för att även uppnå global replikering med låga svarstider. Cosmos DB stöder inte manuella replikeringskommandon.

## <a name="write-concern"></a>Skrivanmärkningar

Vissa program förlitar sig på en [Write Concern](https://docs.mongodb.com/manual/reference/write-concern/) (skrivanmärkning) som anger det antal svar som krävs under en skrivåtgärd. På grund av hur Cosmos DB hanterar replikering i bakgrunden är alla skrivningar automatiskt kvorumskrivningar som standard. Skrivanmärkningar som anges av klientkoden ignoreras. Läs mer om hur du [maximerar tillgänglighet och prestanda med hjälp av konsekvensnivåer](consistency-levels.md).

## <a name="sharding"></a>Horisontell partitionering

Azure Cosmos DB stöder automatisk, horisontell partitionering på serversidan. Den hanterar fragment skapande, placering och balansering automatiskt. Azure Cosmos DB stöder inte manuella sharding-kommandon, vilket innebär att du inte behöver anropa kommandon som addShard, balancerStart, moveChunk etc. Du behöver bara ange fragmentnyckeln när du skapar behållarna eller frågar efter data.

## <a name="sessions"></a>Sessioner

Azure Cosmos DB stöder ännu inte serversessionskommandon.

## <a name="next-steps"></a>Nästa steg

- För ytterligare information kontrollera [Mongo 3.6 version funktioner](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

<sup>Den här artikeln beskriver en funktion i Azure Cosmos DB som tillhandahåller kompatibilitet med trådprotokoll med MongoDB-databaser. Microsoft kör inte MongoDB-databaser för att tillhandahålla den här tjänsten. Azure Cosmos DB är inte anslutet till MongoDB, Inc.</sup>
