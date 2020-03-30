---
title: Partitionering och horisontell skalning i Azure Cosmos DB
description: Lär dig mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitions- och partitionsnycklar och hur du väljer rätt partitionsnyckel för ditt program.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246622"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering och horisontell skalning i Azure Cosmos DB

I den här artikeln beskrivs fysiska och logiska partitioner i Azure Cosmos DB. Den innehåller även bästa praxis för skalning och partitionering. 

## <a name="logical-partitions"></a>Logiska partitioner

En logisk partition består av en uppsättning objekt som har samma partitionsnyckel. I en behållare där alla artiklar `City` till exempel `City` innehåller en egenskap kan du använda som partitionsnyckel för behållaren. Grupper av `City`objekt som `London`har specifika `Paris`värden `NYC`för , till exempel , och bildar distinkta logiska partitioner. Du behöver inte oroa dig för att ta bort en partition när de underliggande data tas bort.

I Azure Cosmos DB är en behållare den grundläggande enheten för skalbarhet. Data som läggs till i behållaren och det dataflöde som du etablerar på behållaren partitioneras automatiskt (horisontellt) över en uppsättning logiska partitioner. Data och dataflöde partitioneras baserat på den partitionsnyckel du anger för Azure Cosmos-behållaren. Mer information finns i [Skapa en Azure Cosmos-behållare](how-to-create-container.md).

En logisk partition definierar också omfattningen av databastransaktioner. Du kan uppdatera objekt i en logisk partition med hjälp av en [transaktion med ögonblicksbildisolering](database-transactions-optimistic-concurrency.md). När nya objekt läggs till i en behållare skapas nya logiska partitioner transparent av systemet.

## <a name="physical-partitions"></a>Fysiska partitioner

En Azure Cosmos-behållare skalas genom att distribuera data och dataflöde över ett stort antal logiska partitioner. Internt mappas en eller flera logiska partitioner till en fysisk partition som består av en uppsättning repliker, även kallade [*replikuppsättning*](global-dist-under-the-hood.md). Varje replikuppsättning är värd för en instans av Azure Cosmos-databasmotorn. En replikuppsättning gör data som lagras i den fysiska partitionen varaktiga, mycket tillgängliga och konsekventa. En fysisk partition stöder den maximala mängden lagrings- och begärandeenheter (RU: er). Varje replik som utgör den fysiska partitionen ärver partitionens lagringskvot. Alla repliker av en fysisk partition stöder tillsammans dataflödet som allokeras till den fysiska partitionen. 

Följande bild visar hur logiska partitioner mappas till fysiska partitioner som distribueras globalt:

![En avbildning som demonstrerar Azure Cosmos DB-partitionering](./media/partition-data/logical-partitions.png)

Dataflödeet som etablerats för en behållare fördelas jämnt mellan fysiska partitioner. En partitionsnyckeldesign som inte distribuerar begäranden om dataflöde jämnt kan skapa "heta" partitioner. Heta partitioner kan resultera i hastighetsbegränsande och ineffektiv användning av det etablerade dataflödet och högre kostnader.

Till skillnad från logiska partitioner är fysiska partitioner en intern implementering av systemet. Du kan inte styra storleken, placeringen eller antalet fysiska partitioner och du kan inte styra mappningen mellan logiska partitioner och fysiska partitioner. Du kan dock styra antalet logiska partitioner och fördelningen av data, arbetsbelastning och dataflöde genom [att välja rätt logisk partitionsnyckel](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [hur du väljer en partitionsnyckel](partitioning-overview.md#choose-partitionkey).
* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
* Lär dig hur du [etablerar dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
