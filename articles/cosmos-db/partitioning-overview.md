---
title: Partitionering i Azure Cosmos DB
description: Lär dig mer om partitionering i Azure Cosmos DB, metod tips när du väljer en partitionsnyckel och hur du hanterar logiska partitioner
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365571"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB

Azure Cosmos DB använder partitionering för att skala enskilda behållare i en databas för att uppfylla ditt programs prestanda behov. I partitionering är objekten i en behållare indelade i distinkta del mängder som kallas *logiska partitioner*. Logiska partitioner skapas baserat på värdet för en *partitionsnyckel* som är associerad med varje objekt i en behållare. Alla objekt i en logisk partition har samma partitionerings nyckel värde.

Till exempel innehåller en behållare objekt. Varje objekt har ett unikt värde för egenskapen `UserID`. Om `UserID` fungerar som partitionsnyckel för objekten i behållaren och det finns 1 000 unika `UserID` värden, skapas 1 000 logiska partitioner för behållaren.

Förutom en partitionsnyckel som avgör objektets logiska partition, har varje objekt i en behållare ett *objekt-ID* (unikt inom en logisk partition). Genom att kombinera partitionsnyckel och objekt-ID skapas objektets *index*, vilket unikt identifierar objektet.

[Att välja en partitionsnyckel](partitioning-overview.md#choose-partitionkey) är ett viktigt beslut som påverkar programmets prestanda.

## <a name="managing-logical-partitions"></a>Hantera logiska partitioner

Azure Cosmos DB transparent och automatiskt hanterar placeringen av logiska partitioner på fysiska partitioner för att effektivt uppfylla behållarens skalbarhet och prestanda krav. När data flödes-och lagrings kraven för en applikation ökar, Azure Cosmos DB flyttar logiska partitioner för att automatiskt sprida belastningen på ett större antal servrar. 

Azure Cosmos DB använder hash-baserad partitionering för att sprida logiska partitioner över fysiska partitioner. Azure Cosmos DB hash-värden för ett objekts partitionsnyckel. Det hashade resultatet avgör den fysiska partitionen. Sedan allokerar Azure Cosmos DB nyckel utrymmet för partitionens nyckel-hashar jämnt över de fysiska partitionerna.

Frågor som har åtkomst till data inom en enda logisk partition är mer kostnads effektivt än frågor som har åtkomst till flera partitioner. Transaktioner (i lagrade procedurer eller utlösare) tillåts bara för objekt i en enda logisk partition.

Mer information om hur Azure Cosmos DB hanterar partitioner finns i [logiska partitioner](partition-data.md). (Det är inte nödvändigt att förstå den interna informationen för att skapa eller köra dina program, men de läggs till här för en nyfiken läsare.)

## <a id="choose-partitionkey"></a>Välja en partitionsnyckel

Följande är en lämplig vägledning för att välja en partitionsnyckel:

* En enda logisk partition har en övre gräns på 20 GB lagrings utrymme.  

* Azure Cosmos-behållare har ett lägsta data flöde på 400 enheter för programbegäran per sekund (RU/s). När data flödet har allokerats i en databas är lägsta ru: er per behållare 100 enheter för programbegäran per sekund (RU/s). Begär anden till samma partitionsnyckel får inte överskrida det data flöde som har allokerats till en partition. Om begär Anden överskrider det allokerade data flödet är begär Anden avgiftsbelagda. Det är därför viktigt att välja en partitionsnyckel som inte resulterar i "aktiva punkter" i ditt program.

* Välj en partitionsnyckel som har ett brett utbud av värden och åtkomst mönster som är jämnt fördelade över logiska partitioner. Detta hjälper till att sprida data och aktiviteten i din behållare över en uppsättning logiska partitioner, så att resurser för data lagring och data flöde kan distribueras mellan de logiska partitionerna.

* Välj en partitionsnyckel som sprider arbets belastningen jämnt över alla partitioner och jämnt över tid. Valet av partitionsnyckel bör balansera behovet av effektiv partitions frågor och transaktioner mot målet med att fördela objekt på flera partitioner för att uppnå skalbarhet.

* Kandidater för partitionsalternativ kan innehålla egenskaper som visas ofta som ett filter i dina frågor. Frågor kan effektivt dirigeras genom att inkludera partitionsnyckel i filtrets predikat.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [partitionering och horisontell skalning i Azure Cosmos DB](partition-data.md).
* Lär dig mer om [etablerade data flöden i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
