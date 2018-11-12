---
title: Partitionering i Azure Cosmos DB
description: Översikt över partitionering i Azure Cosmos DB
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: cc4b165b4fa04e22c9c57547df1657a0c6fc4537
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263535"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB

Partitionering är den teknik som används av Cosmos DB för att skala enskilda behållare i en databas för att uppfylla programmets prestandabehov. Med partitionering kan är objekten i en behållare indelade i olika delmängder som kallas logiska partitioner. Logiska partitioner skapas baserat på värdet för en egenskap för partitionen som som är associerade med varje objekt.

En logisk partition är en delmängd av objekt i en behållare. Objekten i en logisk partition identifieras av partitionsnyckelvärdet som delas av alla objekt i logiska partition.  Anta exempelvis att en behållare som innehåller dokument och varje dokument har en `UserID` egenskapen.  Om `UserID` fungerar som partitionen nyckel för objekt i en behållare och det finns unika 1000 `UserID` värden, 1000 logiska partitioner kommer att skapas för behållaren.

Varje objekt i en behållare har ett **partitionsnyckel** som anger objektets **logisk partition**, och varje objekt har också en **objekt-id** (som är unika inom en logisk partition).  Den **index** av ett objekt identifierar unikt och det bildas genom att kombinera Partitionsnyckeln och objekt-ID: t.

En partitionsnyckel är ett viktigt beslut som påverkar programmets prestanda.  Mer information finns i [en partitionsnyckel](partitioning-overview.md#choose-partitionkey) artikeln för detaljerad information.

## <a name="logical-partition-management"></a>Hantering av logisk partition

Cosmos DB hanterar transparent och automatiskt placeringen av logiska partitioner till fysiska partitioner (serverinfrastruktur) för att effektivt uppfyller behoven för skalbarhet och prestanda för behållaren. När kraven för dataflöde och lagring för programmet ökar flyttar Cosmos DB logiska partitioner för att automatiskt sprida belastningen över ett större antal servrar. Mer information om hur Cosmos DB hanterar partitioner finns [logiska partitioner](partition-data.md) artikeln. Det är inte nödvändigt att förstå dessa information om du vill skapa eller kör dina program.

Cosmos DB använder hash-baserade partitionering för att sprida logiska partitioner över fysiska partitioner.  Partitionsnyckelvärdet för ett objekt hashas av Cosmos DB och det hashade resultatet anger fysisk partition. Cosmos DB allokerar viktiga utrymme på partitionen viktiga hashvärden jämnt över fysiska partitioner ”n”.

Frågor som har åtkomst till data i en enda partition är mer kostnadseffektiva än frågor som har åtkomst till flera partitioner. Transaktioner (i lagrade procedurer eller utlösare) tillåts endast mot objekt i en enda logisk partition.  

## <a id="choose-partitionkey"></a>En partitionsnyckel

Överväg följande information när du väljer en partitionsnyckel:

* En enskild logisk partition tillåts en övre gräns på 10 GB lagringsutrymme.  

* Partitionerad behållare är konfigurerade med minsta dataflöde på 400 RU/s. Begäranden till samma partitionsnyckel får inte överskrida det dataflöde som allokerats till en partition. Om de överskrider det tilldelade dataflödet blir begäranden rate-limited. Det är därför viktigt att välja en partitionsnyckel som inte resulterar i ”hotspots” i ditt program.

* Välj en partitionsnyckel som sprids arbetsbelastning jämnt över alla partitioner och jämnt över tid.  Ditt val av partitionsnyckel bör balansera behovet av frågor för effektiva partition och/eller transaktioner mot avsikten med objekt över flera plattformar för att uppnå skalbarhet.

* Välj en partitionsnyckel som har en mängd värden och mönster i databasåtkomst som fördelas jämnt mellan logiska partitioner. Grundläggande tanken är att sprida data och aktivitet i din behållare över en uppsättning logiska partitioner, så att resurser för lagring och dataflöde som kan distribueras på de logiska partitionerna.

* Kandidater för partitionsnycklar kan innehålla egenskaper som ofta visas som ett filter i dina frågor. Frågor kan dirigeras effektivt genom att inkludera Partitionsnyckeln i filterpredikatet.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [en partitionsnyckel](partitioning-overview.md#choose-partitionkey)
* Lär dig mer om [partitioner](partition-data.md)
* Lär dig mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md)
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md)
