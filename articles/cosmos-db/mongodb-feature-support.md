---
title: "Azure DB Cosmos-funktionsstöd för MongoDB | Microsoft Docs"
description: "Mer information om funktionsstöd Azure Cosmos DB MongoDB API ger för MongoDB 3.4."
services: cosmos-db
author: alekseys
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 007b530cd7a14f063ae4f86d18daa9742c6655c2
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>MongoDB-API-stöd för MongoDB-funktioner och syntax

Azure Cosmos-DB är Microsofts globalt distribuerade flera modellen database-tjänsten. Du kan kommunicera med databasens MongoDB API genom någon av klienten för öppen källkod MongoDB [drivrutiner](https://docs.mongodb.org/ecosystem/drivers). MongoDB-API som möjliggör användning av befintliga klientdrivrutiner genom att följa MongoDB [tråd protokollet](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Med hjälp av Azure Cosmos DB MongoDB API kan du få fördelarna med MongoDB-APIs du ofta, med alla funktioner som Azure Cosmos DB enterprise: [global distributionsplatsen](distribute-data-globally.md), [automatisk delning](partition-data.md), tillgänglighet och svarstid garantier, automatisk indexering av varje fält, kryptering i vila, säkerhetskopior och mycket mer.

## <a name="mongodb-query-language-support"></a>Språkstöd för MongoDB-fråga

Azure Cosmos DB MongoDB API ger omfattande stöd för MongoDB språkkonstruktioner för frågan. Nedan hittar du detaljerad lista över de åtgärder som för närvarande stöds, operatorer, steg, kommandon och alternativ.


## <a name="database-commands"></a>Databasen kommandon

Azure Cosmos-DB stöder följande kommandon för databasen på alla MongoDB-API-konton. 

### <a name="query-and-write-operation-commands"></a>Fråga och Skriv åtgärden-kommandon
- radera
- hitta
- findAndModify
- getLastError
- getMore
- Infoga
- Uppdatering

### <a name="authentication-commands"></a>Kommandon för autentisering
- logga ut
- autentisera
- getnonce

### <a name="administration-commands"></a>Kommandon för administration
- dropDatabase
- listCollections
- ta bort
- skapa
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- ConnectionStatus
- Indexera

### <a name="diagnostics-commands"></a>Diagnostik-kommandon
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Aggregeringen pipeline</a>

Azure Cosmos-DB stöder aggregering pipeline i förhandsversion. Finns den [Azure blogg](https://aka.ms/mongodb-aggregation) anvisningar för hur du integrerar till public preview.

### <a name="aggregation-commands"></a>Aggregeringen kommandon
- mängd
- antal
- Distinkta

### <a name="aggregation-stages"></a>Aggregeringen faser
- $project
- $match
- $limit
- $skip
- $uppnystning
- $group
- $sample
- $sort
- $lookup
- $out
- $count

### <a name="aggregation-expressions"></a>Mängduttryck

#### <a name="boolean-expressions"></a>Booleska uttryck
- $och
- $eller
- $not

#### <a name="set-expressions"></a>Ange uttryck
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Jämförelseuttryck
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetiska uttryck
- $abs
- Lägg till $
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiplicera
- $pow
- $sqrt
- $Subtrahera
- $trunc

#### <a name="string-expressions"></a>Uttryck för anslutningssträng
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Array-uttryck
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $i

#### <a name="date-expressions"></a>Datumuttryck
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $andra
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Villkorsuttryck
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Aggregeringen ackumulatorer
- $sum
- $avg
- $första
- $senaste
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operatorer

Följande operatorer stöds med motsvarande användningsexempel. Överväg att det här exemplet dokumentet som används i frågorna nedan:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operator | Exempel |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$i | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$eller | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$och | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$eller | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$finns | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Anteckningar

Vänster förankrad uttryck kan indexsökning i $regex frågor. Dock med ”i” modifierare (detta) och 'M ' modifierare (multiline) medför samling genomsökningen i alla uttryck.
När det är nödvändigt att innehålla '$' eller ' |', är det bäst att skapa två (eller fler) regex-frågor. Till exempel få följande ursprungliga fråga: ```find({x:{$regex: /^abc$/})```, den måste ändras på följande sätt: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Den första delen använder indexet för att begränsa sökningen till dessa dokument från och med ^ abc och den andra delen matchar exakt samma värden. I fältet operator ' |' fungerar som en ”eller” funktion - frågan ```find({x:{$regex: /^abc|^def/})``` matchar dokument-whin vilket fält 'x' har ett värde som börjar med ”abc” eller ”def”. Om du vill använda indexet rekommenderar vi för att dela frågan i två olika frågor ansluten av $eller operatör: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.


### <a name="geospatial-operators"></a>Geospatiala operatorer

Operator | Exempel 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ja
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ja
$nära | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ja
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ja
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ja
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Ja
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ja
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Ja
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ja
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Ja
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ja

## <a name="additional-operators"></a>Ytterligare operatorer

Operator | Exempel | Anteckningar 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Stöds inte. Använd $regex i stället 

### <a name="methods"></a>Metoder

Följande metoder stöds:

#### <a name="cursor-methods"></a>Markör-metoder

Metod | Exempel | Anteckningar 
--- | --- | --- |
Cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokument utan sorteringsnyckel inte returneras

## <a name="unique-indexes"></a>Unika index

Azure Cosmos-DB indexerar varje fält i dokument som skrivs till databasen som standard. Unika index se till att ett visst fält saknar duplicerade värden i alla dokument i en samling liknande sätt unikhet bevaras för nyckeln som standard ”_id”. Nu kan du skapa anpassade index i Azure Cosmos-databasen med hjälp av kommandot createIndex, inklusive 'unique-begränsningen.

Unikt index är tillgängliga för alla MongoDB-API-konton.

## <a name="time-to-live-ttl"></a>Time-to-live (TTL)

Azure Cosmos-DB stöder en relativ time to live (TTL) baserat på tidsstämpel för dokumentet. TTL-värde kan aktiveras för MongoDB API samlingar via den [Azure-portalen](https://portal.azure.com).

## <a name="user-and-role-management"></a>Hantering av användare och roller

Azure Cosmos-DB stöder ännu inte användare och roller. Azure Cosmos-DB stöder rollbaserad åtkomstkontroll (RBAC) och läs-och skrivbara och skrivskyddade lösenord/nycklar som kan hämtas via den [Azure-portalen](https://portal.azure.com) (anslutningssträngen sidan).

## <a name="replication"></a>Replikering

Azure Cosmos-DB stöder automatisk, ursprunglig replikering på de lägsta lager. Denna logik utökas ut för att uppnå samt låg latens, globala replikering. Azure Cosmos-DB stöder inte manuell replikering kommandon.

## <a name="sharding"></a>Horisontell partitionering

Azure Cosmos-DB stöder automatisk, serversidan horisontell partitionering. Azure Cosmos-DB stöder inte manuell delning kommandon.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använda Studio 3T](mongodb-mongochef.md) med en API för MongoDB-databas.
- Lär dig hur du [använda Robo 3T](mongodb-robomongo.md) med en API för MongoDB-databas.
- Utforska Azure Cosmos DB med Protokollstöd för MongoDB [exempel](mongodb-samples.md).
