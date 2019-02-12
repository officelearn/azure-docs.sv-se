---
title: Etablera dataflöde för Azure Cosmos-behållare och databaser
description: Lär dig hur du ställer in dataflöde för Azure Cosmos-behållare och databaser.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: 439b48c271260e9744bb9c9ca0e2b21e61cf4687
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005071"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Etablera dataflöde på containrar och databaser

En Azure Cosmos-databas är en enhet för hantering för en uppsättning behållare. En databas består av en uppsättning schemaoberoende behållare. En Azure Cosmos-behållare är Faktureringsenhet skalbarhet för både dataflöde och lagring. En behållare är horisontellt partitionerade över en uppsättning datorer i en Azure-region och distribueras över alla Azure-regioner som är associerade med ditt Azure Cosmos-konto.

Du kan konfigurera dataflöde på två Precision med Azure Cosmos DB:
 
- Azure Cosmos-behållare
- Azure Cosmos-databaser

## <a name="set-throughput-on-a-container"></a>Ange dataflöde för en behållare  

Dataflödet som tillhandahållits i ett Azure Cosmos-behållare enbart för behållaren. Behållaren tar emot det etablerade dataflödet som hela tiden. Dataflöde i en behållare har inget serviceavtal med ekonomisk uppbackning av serviceavtal. För att konfigurera dataflödet för en behållare, se [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

Ange dataflöde för en behållare är det vanliga alternativet. Du kan Elastiskt skala dataflöde för en behållare genom att etablera valfritt antal dataflöde med hjälp av programbegäran (ru: er). Men du selektivt kan inte ange genomströmning för logiska partitioner. 

Om arbetsbelastningen som körs på en logisk partition förbrukar mer än det dataflöde som tilldelades till den specifika logisk partitionen, få din verksamhet rate-limited. När hastighetsbegränsande uppstår kan du antingen öka genomflödet för hela behållaren eller försök igen. Mer information om partitionering finns i [logiska partitioner](partition-data.md).

Vi rekommenderar att du konfigurerar dataflöde med behållare precision när du vill att garanterade prestanda för behållaren.

Dataflöde som etablerats på en Azure Cosmos-behållare är jämnt fördelade över alla logiska partitioner i behållaren. Eftersom en eller flera logiska partitioner för en behållare är värd för en fysisk partition, de fysiska partitionerna tillhör exklusivt behållaren och stöd för dataflödet som tillhandahållits för behållaren. 

Följande bild visar hur en fysisk partition är värd för en eller flera logiska partitioner för en behållare:

![Fysisk partition](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Ange dataflöde på en databas

När du etablerar dataflöde i en Azure Cosmos-databas kan delas dataflödet mellan alla behållare i databasen. Ett undantag är om du har angett ett dataflöde på specifika behållare. Dela databasen dataflödet mellan dess behållare är detsamma som värd för en databas på ett kluster med datorer. Eftersom alla behållare i en databas delar resurserna som är tillgängliga på en dator, naturligt ger inte förutsägbar prestanda på en specifik behållare. För att konfigurera dataflöde på en databas, se [konfigurera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

Inställningen dataflöde i en Azure Cosmos-databas garanterar att du får det etablerade dataflödet som hela tiden. Eftersom alla behållare i databasen delar det etablerade dataflödet, ger Azure Cosmos DB inte någon förutsägbart dataflöde garantier för en viss behållare i databasen. Del av vilket dataflöde som kan ta emot en viss behållare är beroende av:

* Antal behållare.
* Val av partitionsnycklar för olika behållare.
* Distribution av arbetsbelastning i olika logiska partitioner av behållarna. 

Vi rekommenderar att du konfigurerar dataflödet för en databas när du vill dela dataflödet över flera behållare, men inte vill att dedikera dataflödet till en viss behållare. 

Följande exempel visar där det har lämpligt att etablera dataflöde på databasnivå:

* Det är användbart för ett program för flera delar dataflöde för en databas på en uppsättning behållare. Varje användare kan representeras av en distinkt Azure Cosmos-behållare.

* Dela dataflöde för en databas i en behållare är användbart när du migrerar en NoSQL-databas, t.ex MongoDB eller Cassandra, finns i ett kluster av virtuella datorer eller från lokala fysiska servrar till Azure Cosmos DB. Tänk på det etablerade dataflödet konfigurerade på din Azure Cosmos-databas som logisk motsvarande, men mer kostnadseffektiv och elastiska med beräkningskapaciteten för din MongoDB- eller Cassandra-kluster.  

Alla behållare som skapas i en databas med etablerat dataflöde måste skapas med en partitionsnyckel. Vid en given tidpunkt tid fördelas det dataflöde som allokeras till en behållare i en databas för alla logiska partitioner för behållaren. När du har en behållare som delar etablerat dataflöde på en databas, kan du selektivt använder dataflödet till en specifik behållare eller en logisk partition. 

Om arbetsbelastningen för en logisk partition förbrukar mer än det dataflöde som tilldelas en specifik logisk partition, är dina åtgärder rate-limited. När hastighetsbegränsande uppstår kan du antingen öka genomflödet för hela behållaren eller försök igen. Mer information om partitionering finns i [logiska partitioner](partition-data.md).

Flera logiska partitioner som delar dataflödet som tillhandahållits till en databas kan finnas på en enda fysisk partition. När en enskild logisk partition för en behållare är alltid begränsade inom en fysisk partition, ”L” logiska partitioner mellan ”C”-behållare som delar det etablerade dataflödet för en databas mappade och finns i ”R” fysiska partitioner. 

Följande bild visar hur en fysisk partition kan vara värd för en eller flera logiska partitioner som hör till olika behållare i en databas:

![Fysisk partition](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Ange dataflöde på en databas och en behållare

Du kan kombinera de två modellerna. Etablering dataflöde på både databasen och behållaren tillåts. I följande exempel visar hur du etablera dataflöde för en Azure Cosmos-databas och en behållare:

* Du kan skapa ett Azure Cosmos-databas med namnet Z med etablerat dataflöde på ”K” ru: er. 
* Därefter skapar fem behållare med namnet A, B, C, D och E i databasen.
* Du kan uttryckligen konfigurera ru: er ”P” för etablerat dataflöde på behållaren med namnet B.
* Dataflödet ”K” ru: er delas mellan de fyra behållarna A, C, D och E. Den exakta mängden dataflöde tillgängliga till A, C, D eller E-varierar. Det finns inga serviceavtal för dataflöden i varje behållare.
* Behållaren med namnet på B är säkert att hämta dataflödet ”P” ru: er hela tiden. Den understöds av serviceavtal.

## <a name="comparison-of-models"></a>Jämförelse av modeller

|**Kvot**  |**Dataflödet som etablerats på en databas**  |**Dataflödet som etableras i en behållare**|
|---------|---------|---------|
|Minsta ru: er |400 (varje ytterligare behållare kräver minst 100 ru: er per sekund efter de första fyra behållarna.) |400|
|Minsta ru: er per behållare|100|400|
|Minsta ru: er som krävs för att använda 1 GB lagringsutrymme|40|40|
|Maximal ru: er|Obegränsade för databasen.|Obegränsade behållare.|
|RU: er tilldelade eller tillgängligt för en specifik behållare|Inga garantier. RU: er som har tilldelats en viss behållare beror på egenskaperna. Egenskaper kan vara valet av partitionsnycklar behållare som delar dataflödet, distribution av arbetsbelastningen och antalet behållare. |Alla ru: er som har konfigurerats på behållaren är enbart för behållaren.|
|Maximalt lagringsutrymme för en behållare|Obegränsad.|Obegränsad.|
|Högsta dataflöde per logisk partition för en behållare|10K ru: er|10K ru: er|
|Maximalt lagringsutrymme (data + index) per logisk partition för en behållare|10 GB|10 GB|

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partition-data.md).
* Lär dig hur du [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

