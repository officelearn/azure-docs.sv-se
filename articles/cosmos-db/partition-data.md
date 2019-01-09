---
title: Partitionering och horisontell skalning i Azure Cosmos DB
description: Läs mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitionera nycklar och hur du väljer rätt Partitionsnyckeln för ditt program.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: dd62e0f4ff110ec8454031f1b66b56025328c33c
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101487"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering och horisontell skalning i Azure Cosmos DB

Den här artikeln förklarar om fysiska och logiska partitioner i Azure Cosmos DB och metodtips för skalning och partitionering. 

## <a name="logical-partitions"></a>Logiska partitioner

En logisk partition består av en uppsättning objekt med samma partitionsnyckel. Anta exempelvis att en behållare där alla objekt innehåller en `City` egenskap, därefter kan du använda `City` som partitionsnyckel för behållaren. Grupper med objekt med specifika värden för den `City` som till exempel, ”London”, ”Paris” ”, NYC” o.s.v. utgör en särskild logisk partition.

I Azure Cosmos DB är en behållare den grundläggande enheten för skalbarhet. Data som lagts till i behållaren och det dataflöde som du etablerar på behållaren är automatiskt (vågrätt) partitionerad över en uppsättning logiska partitioner. De partitioneras baserat på partitionsnyckel som du anger för Cosmos-behållaren. Mer information finns i [så här anger du Partitionsnyckeln för Cosmos-behållaren](how-to-create-container.md) artikeln.

En logisk partition definierar omfattningen för databastransaktioner. Du kan uppdatera objekt i en logisk partition med hjälp av en transaktion med ögonblicksbildisolering. När nya objekt läggs till behållaren, skapa nya logiska partitioner transparent av systemet.

## <a name="physical-partitions"></a>Fysiska partitioner

Azure Cosmos-behållare skalas genom att distribuera data och dataflöde över ett stort antal logiska partitioner. Internt, en eller flera logiska partitioner mappas till en **fysisk partition** som består av en uppsättning repliker kallas även en replikuppsättning. Varje replikuppsättningen har en instans av Azure Cosmos-databasmotorn. En replikuppsättning gör de data som lagras i fysisk partition hållbar, högtillgänglig och konsekvent. En fysisk partition har stöd för en fast, maximal mängd lagringsutrymme och ru: er. Varje replik som består av fysisk partition ärver lagringskvoten. Och alla repliker på en fysisk partition stöd gemensamt för det dataflöde som allokerats till fysisk partition. Följande bild visar logiska partitioner mappas till fysiska partitioner som distribueras globalt:

![Azure Cosmos DB-partitionering](./media/partition-data/logical-partitions.png)

Dataflödet som etableras för en behållare fördelas jämnt mellan de fysiska partitionerna. En partition viktiga designbeslut som inte distribuerar dataflöde begäranden jämnt kan därför skapa ”heta” partitioner. Heta partitioner kan resultera i frekvensbegränsning och ineffektiv användning av det etablerade dataflödet.

Till skillnad från logiska partitioner är fysiska partitioner en intern implementering av systemet. Du kan inte styra sin storlek, placering, antalet eller mappningen mellan logiska partitioner och de fysiska partitionerna. Du kan dock styra antalet logiska partitioner och distribution av data och dataflöde genom att välja rätt Partitionsnyckeln.

## <a name="next-steps"></a>Nästa steg

I den här artikeln tillhandahåller en översikt över Datapartitionering och bästa praxis för skalning och partitionering i Azure Cosmos DB. 

* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md)
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md)
* Lär dig mer om [en partitionsnyckel](partitioning-overview.md#choose-partitionkey)
* Lär dig [hur du etablera dataflöde för en Cosmos-behållare](how-to-provision-container-throughput.md)
* Lär dig [hur du etablera dataflöde för en Cosmos-databas](how-to-provision-database-throughput.md)
