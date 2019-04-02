---
title: Partitionering i Azure Cosmos DB
description: Översikt över partitionering i Azure Cosmos DB.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: e88be8e7b94566ff94dd94a8679f8ade9d54c0b6
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762334"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB

Azure Cosmos DB använder partitionering för att skala enskilda behållare i en databas att uppfylla programmets prestandabehov. Vid partitionering, objekten i en behållare är indelade i olika delmängder som kallas *logiska partitioner*. Logiska partitioner bildas baserat på värdet för en *partitionsnyckel* som är associerad med varje objekt i en behållare. Alla objekt i en logisk partition har samma partitionsnyckelvärde.

Till exempel en behållare som innehåller objekt. Varje objekt har ett unikt värde för den `UserID` egenskapen. Om `UserID` fungerar som partitionen nyckel för objekt i behållaren och 1 000 är unika `UserID` värden, 1 000 logiska partitioner har skapats för behållaren.

Förutom en partitionsnyckel som anger objektets logisk partition, varje objekt i en behållare har ett *objektet ID* (unika inom en logisk partition). Kombinera Partitionsnyckeln och objekt-ID: T skapar objektets *index*, som unikt identifierar objektet.

[En partitionsnyckel](partitioning-overview.md#choose-partitionkey) är ett viktigt beslut som påverkar programmets prestanda.

## <a name="managing-logical-partitions"></a>Hantera logiska partitioner

Azure Cosmos DB hanterar transparent och automatiskt placeringen av logiska partitioner på fysiska partitioner för att effektivt uppfyller behoven för skalbarhet och prestanda för behållaren. När behovet av dataflöde och lagring för ett program ökar flyttar logiska partitioner för att automatiskt sprida belastningen över ett större antal servrar i Azure Cosmos DB. 

Azure Cosmos DB använder hash-baserade partitionering för att sprida logiska partitioner över fysiska partitioner. Azure Cosmos DB hashar partitionsnyckelvärdet för ett objekt. Det hashade resultatet anger fysisk partition. Sedan kan allokerar Azure Cosmos DB viktiga utrymme på partitionen viktiga hashvärden jämnt över fysiska partitioner.

Frågor som har åtkomst till data i en enda logisk partition är mer kostnadseffektiva än frågor som har åtkomst till flera partitioner. Transaktioner (i lagrade procedurer eller utlösare) tillåts endast mot objekt i en enda logisk partition.

Mer information om hur Azure Cosmos DB hanterar partitioner finns [logiska partitioner](partition-data.md). (Det är inte nödvändigt att förstå interna information som du vill skapa eller kör dina program, men finns här en nyfiken läsare.)

## <a id="choose-partitionkey"></a>En partitionsnyckel

Följande är en bra vägledning för att välja en partitionsnyckel:

* En enskild logisk partition har en övre gräns på 10 GB lagringsutrymme.  

* Azure Cosmos-behållare har ett minsta dataflöde på 400 begäransenheter per sekund (RU/s). Begäranden till samma partitionsnyckel får inte överskrida det dataflöde som har allokerats till en partition. Om begäranden överskrider det tilldelade dataflödet är begärandena rate-limited. Det är därför viktigt att välja en partitionsnyckel som inte resulterar i ”hotspots” i ditt program.

* Välj en partitionsnyckel som har en mängd värden och mönster i databasåtkomst som fördelas jämnt mellan logiska partitioner. Detta hjälper att sprida data och aktivitet i din behållare över uppsättningen logiska partitioner så att resurser för lagring och dataflöde som kan distribueras på de logiska partitionerna.

* Välj en partitionsnyckel som sprider ut belastningen jämnt över alla partitioner och jämnt över tid. Ditt val av partitionsnyckel bör balansera behovet av effektiv partition frågor och transaktioner mot avsikten med objekt över flera plattformar för att uppnå skalbarhet.

* Kandidater för partitionsnycklar kan innehålla egenskaper som ofta visas som ett filter i dina frågor. Frågor kan dirigeras effektivt genom att inkludera Partitionsnyckeln i filterpredikatet.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [partitionering och horisontell skalning i Azure Cosmos DB](partition-data.md).
* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
