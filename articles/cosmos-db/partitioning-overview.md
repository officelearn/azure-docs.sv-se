---
title: Partitionering i Azure Cosmos DB
description: Lär dig mer om partitionering i Azure Cosmos DB, metodtips när du väljer en partitionsnyckel och hur du hanterar logiska partitioner
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251874"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB

Azure Cosmos DB använder partitionering för att skala enskilda behållare i en databas för att uppfylla prestandabehoven för ditt program. Vid partitionering delas objekten i en behållare in i olika delmängder som kallas *logiska partitioner*. Logiska partitioner bildas baserat på värdet för en *partitionsnyckel* som är associerad med varje objekt i en behållare. Alla objekt i en logisk partition har samma partitionsnyckelvärde.

En behållare innehåller till exempel artiklar. Varje objekt har ett `UserID` unikt värde för egenskapen. Om `UserID` fungerar som partitionsnyckel för objekten i behållaren och det `UserID` finns 1 000 unika värden skapas 1 000 logiska partitioner för behållaren.

Förutom en partitionsnyckel som bestämmer objektets logiska partition har varje objekt i en behållare ett *objekt-ID* (unikt inom en logisk partition). Genom att kombinera partitionsnyckeln och artikel-ID:t skapas objektets *index*, som unikt identifierar objektet.

[Att välja en partitionsnyckel](partitioning-overview.md#choose-partitionkey) är ett viktigt beslut som påverkar programmets prestanda.

## <a name="managing-logical-partitions"></a>Hantera logiska partitioner

Azure Cosmos DB hanterar transparent och automatiskt placeringen av logiska partitioner på fysiska partitioner för att effektivt uppfylla behållarens skalbarhets- och prestandabehov. När dataflödet och lagringskraven för ett program ökar flyttar Azure Cosmos DB logiska partitioner för att automatiskt sprida belastningen över ett större antal servrar. 

Azure Cosmos DB använder hash-baserad partitionering för att sprida logiska partitioner över fysiska partitioner. Azure Cosmos DB hashar partitionsnyckelvärdet för ett objekt. Hash-resultatet bestämmer den fysiska partitionen. Azure Cosmos DB allokerar sedan det viktigaste utrymmet för partitionsnyckeln hashar jämnt över de fysiska partitionerna.

Frågor som kommer åt data i en enda logisk partition är mer kostnadseffektiva än frågor som kommer åt flera partitioner. Transaktioner (i lagrade procedurer eller utlösare) tillåts endast mot artiklar i en enda logisk partition.

Mer information om hur Azure Cosmos DB hanterar partitioner finns i [Logiska partitioner](partition-data.md). (Det är inte nödvändigt att förstå de interna detaljerna för att bygga eller köra dina program, men läggs här för en nyfiken läsare.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Välja en partitionsnyckel

Följande är en bra vägledning för att välja en partitionsnyckel:

* En enda logisk partition har en övre gräns på 20 GB lagringsutrymme.  

* Azure Cosmos-behållare har ett minimum dataflöde på 400 begärandeenheter per sekund (RU/s). När dataflöde etableras i en databas är minimienheter per behållare 100 begärandeenheter per sekund (RU/s). Begäranden till samma partitionsnyckel kan inte överskrida dataflödet som allokeras till en partition. Om begäranden överskrider det tilldelade dataflödet är begäranden räntebegränsade. Så det är viktigt att välja en partitionsnyckel som inte resulterar i "hotspots" i ditt program.

* Välj en partitionsnyckel som har ett brett spektrum av värden och åtkomstmönster som är jämnt fördelade över logiska partitioner. Detta hjälper till att sprida data och aktiviteten i behållaren över uppsättningen logiska partitioner, så att resurser för datalagring och dataflöde kan distribueras över de logiska partitionerna.

* Välj en partitionsnyckel som sprider arbetsbelastningen jämnt över alla partitioner och jämnt över tiden. Ditt val av partitionsnyckel bör balansera behovet av effektiva partitionsfrågor och transaktioner mot målet att distribuera objekt över flera partitioner för att uppnå skalbarhet.

* Kandidater för partitionsnycklar kan innehålla egenskaper som ofta visas som ett filter i dina frågor. Frågor kan dirigeras effektivt genom att inkludera partitionsnyckeln i filterpredicatet.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [partitionering och vågrät skalning i Azure Cosmos DB](partition-data.md).
* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
