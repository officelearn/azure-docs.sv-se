---
title: Partitionering och horisontell skalning i Azure Cosmos DB
description: Läs mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitionera nycklar och hur du väljer rätt Partitionsnyckeln för ditt program.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 9e75068a1e05f12c7b887b601777227902f15ed8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238584"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering och horisontell skalning i Azure Cosmos DB

Den här artikeln förklarar om fysiska och logiska partitioner i Azure Cosmos DB och metodtips för skalning och partitionering. 

## <a name="logical-partitions"></a>Logiska partitioner

En logisk partition består av en uppsättning objekt med samma partitionsnyckel. Anta exempelvis att en behållare där alla objekt innehåller en `City` egenskap, därefter kan du använda `City` som partitionsnyckel för behållaren. Grupper med objekt med specifika värden för den `City` som till exempel, ”London”, ”Paris” ”, NYC” o.s.v. utgör en särskild logisk partition.

I Azure Cosmos DB är en behållare den grundläggande enheten för skalbarhet. Data som lagts till i behållaren och det dataflöde som du etablerar på behållaren är automatiskt (vågrätt) partitionerad över en uppsättning logiska partitioner. De partitioneras baserat på partitionsnyckel som du anger för Cosmos-behållaren. Mer information finns i [så här anger du Partitionsnyckeln för Cosmos-behållaren](how-to-create-container.md) artikeln.

En logisk partition definierar omfattningen för databastransaktioner. Du kan uppdatera objekt i en logisk partition med hjälp av en transaktion med ögonblicksbildisolering.

När nya objekt läggs till i behållaren eller om du ökar dataflödet som tillhandahållits för behållaren, skapa nya logiska partitioner transparent av systemet.

## <a name="physical-partitions"></a>Fysiska partitioner

En Cosmos-behållare skalas genom att distribuera data och dataflöde över ett stort antal logiska partitioner. Internt, en eller flera logiska partitioner mappas till en **resurspartition** som består av en uppsättning repliker kallas även en replikuppsättning. Varje replikuppsättningen har en instans av databasmotorn Cosmos. En replikuppsättning gör de data som lagras i resurspartition hållbar, högtillgänglig och konsekvent. En resurspartition har stöd för en fast, maximal mängd lagringsutrymme och ru: er. Varje replik som består av resurspartition ärver lagringskvoten. Och alla repliker av en resurspartition stöd gemensamt för det dataflöde som tilldelats resursen partitionen. Följande bild visar logiska partitioner mappas till resurspartitioner som distribueras globalt:

![Azure Cosmos DB-partitionering](./media/partition-data/logical-partitions.png)

Dataflödet som etableras för en behållare fördelas jämnt mellan resurspartitioner. En partition viktiga designbeslut som inte distribuerar dataflöde begäranden jämnt kan därför skapa ”heta” partitioner. Heta partitioner kan resultera i frekvensbegränsning och ineffektiv användning av det etablerade dataflödet.

Till skillnad från logiska partitioner är resurspartitioner en intern implementering av systemet. Du kan inte styra sin storlek, placering, antalet eller mappningen mellan logiska partitioner och resurspartitioner. Du kan dock styra antalet logiska partitioner och distribution av data och dataflöde genom att välja rätt Partitionsnyckeln.

## <a name="next-steps"></a>Nästa steg

I den här artikeln tillhandahåller en översikt över Datapartitionering och bästa praxis för skalning och partitionering i Azure Cosmos DB. 

* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md)
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md)
* Lär dig mer om [en partitionsnyckel](partitioning-overview.md#choose-partitionkey)
* Lär dig [hur du etablera dataflöde för en Cosmos-behållare](how-to-provision-container-throughput.md)
* Lär dig [hur du etablera dataflöde för en Cosmos-databas](how-to-provision-database-throughput.md)
