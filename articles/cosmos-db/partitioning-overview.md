---
title: Partitionering i Azure Cosmos DB
description: Lär dig mer om partitionering i Azure Cosmos DB, metod tips när du väljer en partitionsnyckel och hur du hanterar logiska partitioner
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 1a760b4cedad5e43a2ef9f186162675aaf6d5ea5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234187"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionering i Azure Cosmos DB

Azure Cosmos DB använder partitionering för att skala enskilda behållare i en databas för att uppfylla ditt programs prestanda behov. I partitionering är objekten i en behållare indelade i distinkta del mängder som kallas *logiska partitioner*. Logiska partitioner skapas baserat på värdet för en *partitionsnyckel* som är associerad med varje objekt i en behållare. Alla objekt i en logisk partition har samma partitionerings nyckel värde.

Till exempel innehåller en behållare objekt. Varje objekt har ett unikt värde för `UserID` egenskapen. Om `UserID` fungerar som partitionsnyckel för objekten i behållaren och det finns 1 000 unika `UserID` värden, skapas 1 000 logiska partitioner för behållaren.

Förutom en partitionsnyckel som avgör objektets logiska partition, har varje objekt i en behållare ett *objekt-ID* (unikt inom en logisk partition). Genom att kombinera partitionsnyckel och *objekt-ID* skapas objektets *index*, vilket unikt identifierar objektet.

[Att välja en partitionsnyckel](partitioning-overview.md#choose-partitionkey) är ett viktigt beslut som påverkar programmets prestanda.

## <a name="managing-logical-partitions"></a>Hantera logiska partitioner

Azure Cosmos DB transparent och automatiskt hanterar placeringen av logiska partitioner på fysiska partitioner för att effektivt uppfylla behållarens skalbarhet och prestanda krav. När data flödes-och lagrings kraven för en applikation ökar, Azure Cosmos DB flyttar logiska partitioner för att automatiskt sprida belastningen över ett större antal fysiska partitioner. Du kan läsa mer om [fysiska partitioner](partition-data.md#physical-partitions).

Azure Cosmos DB använder hash-baserad partitionering för att sprida logiska partitioner över fysiska partitioner. Azure Cosmos DB hash-värden för ett objekts partitionsnyckel. Det hashade resultatet avgör den fysiska partitionen. Sedan allokerar Azure Cosmos DB nyckel utrymmet för partitionens nyckel-hashar jämnt över de fysiska partitionerna.

Transaktioner (i lagrade procedurer eller utlösare) tillåts bara för objekt i en enda logisk partition.

Du kan lära dig mer om [hur Azure Cosmos DB hanterar partitioner](partition-data.md). (Det är inte nödvändigt att förstå den interna informationen för att skapa eller köra dina program, men de läggs till här för en nyfiken läsare.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Välja en partitionsnyckel

Att välja partitionsnyckel är ett enkelt men viktigt design val i Azure Cosmos DB. När du har valt partitionsnyckel är det inte möjligt att ändra den på plats. Om du behöver ändra din partitionsnyckel bör du flytta dina data till en ny behållare med din nya önskade partitionsnyckel.

För **alla** behållare bör din partitionsnyckel:

* Vara en egenskap som har ett värde som inte ändras. Om en egenskap är partitionsnyckel kan du inte uppdatera egenskapens värde.
* Ha en hög kardinalitet. Med andra ord bör egenskapen ha ett brett utbud av möjliga värden.
* Förbruknings enhet för begäran (RU) och data lagring jämnt över alla logiska partitioner. Detta säkerställer även RU-förbrukning och lagrings distribution över dina fysiska partitioner.

Om du behöver [transaktioner med flera objekt](database-transactions-optimistic-concurrency.md#multi-item-transactions) i Azure Cosmos DB måste du använda [lagrade procedurer eller utlösare](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Alla JavaScript-baserade lagrade procedurer och utlösare är begränsade till en enda logisk partition.

## <a name="partition-keys-for-read-heavy-containers"></a>Partitionsnyckel för Read-tung containrar

För de flesta behållare är ovanstående kriterier allt du behöver tänka på när du väljer en partitionsnyckel. För stora, omfattande behållare kanske du vill välja en partitionsnyckel som visas ofta som ett filter i dina frågor. Frågor kan [effektivt dirigeras till relevanta fysiska partitioner](how-to-query-container.md#in-partition-query) genom att inkludera partitionsnyckel i filtrets predikat.

Om de flesta av dina arbets belastnings begär Anden är frågor och de flesta av dina frågor har ett likhets filter för samma egenskap, kan den här egenskapen vara ett bra alternativ för partitionsnyckel. Om du till exempel ofta kör en fråga som filtrerar på `UserID`så minskar antalet `UserID` [frågor om flera partitioner](how-to-query-container.md#avoiding-cross-partition-queries)genom att välja som partitionsnyckel.

Men om din behållare är liten har du förmodligen inte tillräckligt med fysiska partitioner för att behöva oroa dig över prestanda påverkan för frågor över olika partitioner. De flesta små behållare i Azure Cosmos DB behöver bara en eller två fysiska partitioner.

Om din behållare kan växa till fler än ett fåtal fysiska partitioner, bör du se till att du väljer en partitionsnyckel som minimerar frågor om flera partitioner. Din behållare kräver mer än några få fysiska partitioner när något av följande stämmer:

* Din behållare kommer att ha över 30 000 RU-etableringen
* Behållaren kommer att lagra över 100 GB data

## <a name="using-item-id-as-the-partition-key"></a>Använda objekt-ID som partitionsnyckel

Om din behållare har en egenskap som har ett brett utbud av möjliga värden, är det troligt vis ett utmärkt partitionerings nyckel val. Ett möjligt exempel på en sådan egenskap är *objekt-ID*. För små Read-tung containrar eller Write-tungt behållare i valfri storlek är *objekt-ID: t* naturligt ett bra val för partitionsnyckel.

Systemets egenskaps *objekt-ID* är garanterat att det finns i varje objekt i din Cosmos-behållare. Du kan ha andra egenskaper som representerar ett logiskt ID för ditt objekt. I många fall är dessa också fantastiska partitionsalternativ av olika anledningar som *objekt-ID*.

*Objekt-ID* är ett bra partitionerings val av följande orsaker:

* Det finns ett brett utbud av möjliga värden (ett unikt *objekt-ID* per objekt).
* Eftersom det finns ett unikt *objekt-ID* per objekt, gör *objekt-ID: t* ett bra jobb till jämn balansering av ru-förbrukning och data lagring.
* Du kan enkelt göra effektiva Poäng läsningar eftersom du alltid känner till ett objekts partitionsnyckel om du känner till dess *objekt-ID*.

Några saker att tänka på när du väljer *objekt-ID: t* som partitionsnyckel är:

* Om *objekt-ID: t* är partitionsnyckel, blir det en unik identifierare i hela behållaren. Du kan inte ha objekt som har en dubblett av *objekt-ID*.
* Om du har en Läs intensiv behållare som har många [fysiska partitioner](partition-data.md#physical-partitions), blir frågorna mer effektiva om de har ett likhets filter med *objekt-ID*.
* Du kan inte köra lagrade procedurer eller utlösare över flera logiska partitioner.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [partitionering och horisontell skalning i Azure Cosmos DB](partition-data.md).
* Lär dig mer om [etablerade data flöden i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
