---
title: Köra frågor mot containrar i Azure Cosmos DB
description: Lär dig hur du frågar behållare i Azure Cosmos DB med hjälp av frågor om partitionering och partitionering
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131402"
---
# <a name="query-an-azure-cosmos-container"></a>Köra frågor mot en Azure Cosmos-container

Den här artikeln beskriver hur du kör frågor mot en container (samling, graf eller tabell) i Azure Cosmos DB. I synnerhet täcker det hur frågor i partition och korspartitioner fungerar i Azure Cosmos DB.

## <a name="in-partition-query"></a>Frågekörning inom en partition

När du frågar data från behållare, om frågan har ett partitionsnyckelfilter angivet, optimerar Azure Cosmos DB automatiskt frågan. Frågan dirigeras till de [fysiska partitioner](partition-data.md#physical-partitions) som motsvarar de partitionsnyckelvärden som anges i filtret.

Tänk till exempel på nedanstående fråga `DeviceId`med ett likhetsfilter på . Om vi kör den här frågan `DeviceId`på en behållare som partitioneras på filtreras den här frågan till en enda fysisk partition.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Precis som i det tidigare exemplet filtreras den här frågan också till en enda partition. Om du lägger till det extra filtret på `Location` ändras inte följande:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Här är en fråga som har ett intervallfilter på partitionsnyckeln och som inte begränsas till en enda fysisk partition. För att vara en fråga i partitionen måste frågan ha ett likhetsfilter som innehåller partitionsnyckeln:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Frågekörning mellan partitioner

Följande fråga har inget filter på partitionsnyckeln (`DeviceId`). Därför måste det fläkta ut till alla fysiska partitioner där det körs mot varje partition index:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Varje fysisk partition har sitt eget index. När du kör en korspartitionsfråga på en behållare kör du därför effektivt en fråga *per* fysisk partition. Azure Cosmos DB sammanställer automatiskt resultat över olika fysiska partitioner.

Indexen i olika fysiska partitioner är oberoende av varandra. Det finns inget globalt index i Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Parallell frågekörning mellan partitioner

Azure Cosmos DB SDK:erna 1.9.0 och senare stöder alternativ för parallell frågekörning. Parallell frågekörning mellan partitioner gör att du kan genomföra frågor med låg latens mellan partitioner.

Du kan hantera parallell frågekörning genom att justera följande parametrar:

- **MaxConcurrency**: Anger det maximala antalet samtidiga nätverksanslutningar till behållarens partitioner. Om du ställer `-1`in den här egenskapen på hanterar SDK graden av parallellitet. Om `MaxConcurrency` uppsättningen `0`till finns det en enda nätverksanslutning till behållarens partitioner.

- **MaxBufferedItemCount**: Avväger frågesvarstid kontra minnesanvändning på klientsidan. Om det här alternativet utelämnas eller anges till -1 hanterar SDK:n antalet objekt som buffras under en parallell frågekörning.

På grund av Azure Cosmos DB:s förmåga att parallellisera frågor mellan partitioner skalar frågefördröjningen i allmänhet, liksom systemet lägger till [fysiska partitioner](partition-data.md#physical-partitions). RU-avgiften kommer dock att öka avsevärt i takt med att det totala antalet fysiska partitioner ökar.

När du kör en fråga över partitioner gör du i huvudsak en separat fråga per enskild fysisk partition. Även om frågor mellan partitionsfrågor använder indexet, om det är tillgängligt, är de fortfarande inte alls lika effektiva som frågor i partitionen.

## <a name="useful-example"></a>Användbart exempel

Här är en analogi för att bättre förstå korspartitionsfrågor:

Låt oss föreställa oss att du är en leverans förare som har att leverera paket till olika lägenhetskomplex. Varje lägenhetskomplex har en lista på området som har alla de boendes enhetsnummer. Vi kan jämföra varje lägenhetskomplex med en fysisk partition och varje lista med den fysiska partitionens index.

Vi kan jämföra frågor i partition och korspartitioner med det här exemplet:

### <a name="in-partition-query"></a>Frågekörning inom en partition

Om leveransföraren känner till rätt lägenhetskomplex (fysisk partition), kan de omedelbart köra till rätt byggnad. Föraren kan kontrollera lägenhetskomplexets lista över den boendes enhetsnummer (indexet) och snabbt leverera lämpliga paket. I detta fall slösar föraren inte någon tid eller ansträngning att köra till ett lägenhetskomplex för att kontrollera och se om några paketmottagare bor där.

### <a name="cross-partition-query-fan-out"></a>Fråga över partitionering (utfläktning)

Om leveransdrivrutinen inte vet rätt lägenhetskomplex (fysisk partition), måste de köra till varenda hyreshus och kontrollera listan med alla de boendes enhetsnummer (indexet). När de anländer till varje lägenhetskomplex, kommer de fortfarande att kunna använda listan över adresserna till varje invånare. De måste dock kontrollera varje lägenhetskomplexs lista, oavsett om några paketmottagare bor där eller inte. Så här fungerar frågor mellan partitioner. Även om de kan använda indexet (behöver inte knacka på varje enskild dörr), måste de separat kontrollera indexet för varje fysisk partition.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Fråga över partition (begränsad till endast ett fåtal fysiska partitioner)

Om leveransföraren vet att alla paketmottagare bor i ett fåtal lägenhetskomplex, behöver de inte köra till varenda en. Under körning till några lägenhetskomplex kommer fortfarande att kräva mer arbete än att besöka bara en enda byggnad, sparar leverans föraren fortfarande betydande tid och ansträngning. Om en fråga har partitionsnyckeln `IN` i filtret med nyckelordet, kontrollerar den bara den relevanta fysiska partitionens index för data.

## <a name="avoiding-cross-partition-queries"></a>Undvika frågor mellan partitioner

För de flesta behållare är det oundvikligt att du kommer att ha några korspartitionsfrågor. Med några korspartitionsfrågor är ok! Nästan alla frågeåtgärder stöds över partitioner (både logiska partitionsnycklar och fysiska partitioner). Azure Cosmos DB har också många optimeringar i frågemotorn och klient-SDK:er för att parallellisera frågekörning över fysiska partitioner.

För de flesta lästunga scenarier rekommenderar vi att du helt enkelt väljer den vanligaste egenskapen i frågefiltren. Du bör också se till att partitionsnyckeln följer andra metodtips för [val av partitionsnyckel](partitioning-overview.md#choose-partitionkey).

Undvika korspartitionsfrågor vanligtvis bara frågor med stora behållare. Du debiteras minst cirka 2,5 RU:s varje gång du kontrollerar en fysisk partitions index för resultat, även om inga objekt i den fysiska partitionen matchar frågans filter. Om du bara har en (eller bara några) fysiska partitioner kommer korspartitionsfrågor inte att förbruka betydligt fler RU:s än i partitionsfrågor.

Antalet fysiska partitioner är knutet till mängden avstämmda RU:er. Varje fysisk partition tillåter upp till 10 000 etablerade RU:er och kan lagra upp till 50 GB data. Azure Cosmos DB hanterar automatiskt fysiska partitioner åt dig. Antalet fysiska partitioner i behållaren är beroende av ditt etablerade dataflöde och förbrukad lagring.

Du bör försöka undvika frågor mellan partitioner om din arbetsbelastning uppfyller villkoren nedan:
- Du planerar att ha över 30 000 RU:s avsättningar
- Du planerar att lagra över 100 GB data

## <a name="next-steps"></a>Nästa steg

I följande artiklar lär du dig mer om partitionering i Azure Cosmos DB:

- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
- [Syntetiska partitionsnycklar i Azure Cosmos DB](synthetic-partition-keys.md)
