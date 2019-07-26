---
title: Partitionering och vågrät skalning i Azure Cosmos DB
description: Lär dig mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitionerings nycklar och hur du väljer rätt partitionsnyckel för ditt program.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 1b624270fe22004a6ae64affe87b2fc22a2e9a66
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467689"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering och vågrät skalning i Azure Cosmos DB

I den här artikeln beskrivs fysiska och logiska partitioner i Azure Cosmos DB. Den innehåller också metod tips för skalning och partitionering. 

## <a name="logical-partitions"></a>Logiska partitioner

En logisk partition består av en uppsättning objekt som har samma partitionsnyckel. I en behållare där alla objekt innehåller en `City` egenskap kan du till exempel använda `City` som partitionsnyckel för behållaren. Grupper av objekt som har specifika värden för `City`, till exempel `London`, `Paris`, och `NYC`som utgör distinkta logiska partitioner. Du behöver inte oroa dig för att ta bort en partition när underliggande data tas bort.

I Azure Cosmos DB är en behållare den grundläggande enheten för skalbarhet. Data som läggs till i behållaren och det data flöde som du etablerar på behållaren är automatiskt (vågrätt) partitionerade i en uppsättning logiska partitioner. Data och data flöde partitioneras baserat på den partitionsnyckel som du anger för Azure Cosmos-behållaren. Mer information finns i [skapa en Azure Cosmos-behållare](how-to-create-container.md).

En logisk partition definierar även omfattningen av databas transaktioner. Du kan uppdatera objekt i en logisk partition med hjälp av en [transaktion med ögonblicks bild isolering](database-transactions-optimistic-concurrency.md). När nya objekt läggs till i en behållare, skapas nya logiska partitioner transparent av systemet.

## <a name="physical-partitions"></a>Fysiska partitioner

En Azure Cosmos-behållare skalas genom att distribuera data och data flöde över ett stort antal logiska partitioner. Internt mappas en eller flera logiska partitioner till en fysisk partition som består av en uppsättning repliker, även kallat en [*replik uppsättning*](global-dist-under-the-hood.md). Varje replik uppsättning är värd för en instans av Azure Cosmos DB databas motorn. En replik uppsättning gör data lagrade i den fysiska partitionen tåligt, hög tillgängliga och konsekvent. En fysisk partition har stöd för maximalt lagrings utrymme och enheter för programbegäran (ru: er). Varje replik som utgör den fysiska partitionen ärver partitionens lagrings kvot. Alla repliker av en fysisk partition har gemensamt stöd för det data flöde som har allokerats till den fysiska partitionen. 

Följande bild visar hur logiska partitioner mappas till fysiska partitioner som distribueras globalt:

![En bild som visar Azure Cosmos DB partitionering](./media/partition-data/logical-partitions.png)

Det data flöde som har allokerats för en behållare är jämnt fördelat mellan fysiska partitioner. En partitionsnyckel som inte distribuerar data flödes förfrågningar jämnt kan skapa "varma" partitioner. Varma partitioner kan leda till hastighets begränsning och ineffektiv användning av det etablerade data flödet och högre kostnader.

Till skillnad från logiska partitioner är fysiska partitioner en intern implementering av systemet. Du kan inte styra storlek, placering eller antal fysiska partitioner och du kan inte styra mappningen mellan logiska partitioner och fysiska partitioner. Du kan dock styra antalet logiska partitioner och distributionen av data, arbets belastning och data flöde genom [att välja rätt logisk partitionsnyckel](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [att välja en partitionsnyckel](partitioning-overview.md#choose-partitionkey).
* Lär dig mer om [etablerade data flöden i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
