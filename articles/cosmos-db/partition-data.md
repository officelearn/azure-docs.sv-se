---
title: Partitionering och horisontell skalning i Azure Cosmos DB
description: Läs mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitionera nycklar och hur du väljer rätt Partitionsnyckeln för ditt program.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797831"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering och horisontell skalning i Azure Cosmos DB

Den här artikeln förklarar fysiska och logiska partitioner i Azure Cosmos DB. Det innehåller också information om metodtips för skalning och partitionering. 

## <a name="logical-partitions"></a>Logiska partitioner

En logisk partition består av en uppsättning objekt som har samma partitionsnyckel. Till exempel i en behållare där alla objekt som innehåller en `City` egenskapen, som du kan använda `City` som partitionsnyckel för behållaren. Grupper med objekt som har specifika värden för `City`, till exempel `London`, `Paris`, och `NYC`, utgör distinkta logiska partitioner. Du behöver bekymra dig om att ta bort en partition när underliggande data tas bort.

I Azure Cosmos DB är en behållare den grundläggande enheten för skalbarhet. Data som har lagts till i behållaren och det dataflöde som du etablerar på behållaren är automatiskt (vågrätt) partitionerad över en uppsättning logiska partitioner. Data och dataflöde som partitioneras baserat på partitionsnyckel som du anger för Azure Cosmos-behållaren. Mer information finns i [skapa en Azure Cosmos-behållare](how-to-create-container.md).

En logisk partition definierar också omfånget för databastransaktioner. Du kan uppdatera objekt i en logisk partition med hjälp av en [transaktion med ögonblicksbildisolering](database-transactions-optimistic-concurrency.md). När nya objekt läggs till i en behållare, skapa nya logiska partitioner transparent av systemet.

## <a name="physical-partitions"></a>Fysiska partitioner

En Azure Cosmos-behållare skalas genom att distribuera data och dataflöde över ett stort antal logiska partitioner. Internt, en eller flera logiska partitioner mappas till en fysisk partition som består av en uppsättning repliker, kallas även en [ *replikuppsättningen*](global-dist-under-the-hood.md). Varje replik ange värdar en instans av Azure Cosmos DB database engine. En replikuppsättning gör de data som lagras i fysisk partition hållbar, högtillgänglig och konsekvent. En fysisk partition har stöd för den maximala mängden lagringsutrymme och begäransenheter (ru: er). Varje replik som utgör fysisk partition ärver den partitionen lagringskvoten. Alla repliker på en fysisk partition stöder sammantaget dataflödet som allokerats till fysisk partition. 

Följande bild visar logiska partitioner mappas till fysiska partitioner som distribueras globalt:

![En bild som visar Azure Cosmos DB partitionering](./media/partition-data/logical-partitions.png)

Dataflödet som etableras för en behållare fördelas jämnt mellan fysiska partitioner. En partition viktiga designbeslut som inte distribuerar dataflöde begäranden jämnt kan skapa ”heta” partitioner. Heta partitioner kan resultera i frekvensbegränsning och ineffektiv användning av dataflöde och högre kostnader.

Till skillnad från logiska partitioner är fysiska partitioner en intern implementering av systemet. Du kan inte styra placering, storlek och antal fysiska partitioner och du kan inte styra mappningen mellan logiska partitioner och fysiska partitioner. Du kan dock styra antalet logiska partitioner och distributionen av data, arbetsbelastning och dataflöde av [välja rätt logiska Partitionsnyckeln](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [en partitionsnyckel](partitioning-overview.md#choose-partitionkey).
* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
* Lär dig hur du [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
