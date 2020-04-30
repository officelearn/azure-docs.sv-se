---
title: Partitionering och horisontell skalning i Azure Cosmos DB
description: Lär dig mer om hur partitionering fungerar i Azure Cosmos DB, hur du konfigurerar partitionering och partitionerings nycklar och hur du väljer rätt partitionsnyckel för ditt program.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 19e4c61ba930bb9b127e2401174bcea3fd240dce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234236"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering och horisontell skalning i Azure Cosmos DB

Den här artikeln förklarar förhållandet mellan logiska och fysiska partitioner. Den innehåller också metod tips för partitionering och ger en djupgående vy med hur horisontell skalning fungerar i Azure Cosmos DB. Du behöver inte känna till dessa interna uppgifter för att [välja partitionsnyckel](partitioning-overview.md#choose-partitionkey) , men vi har täckt dem så att du har klarhet om hur Azure Cosmos DB fungerar.

## <a name="logical-partitions"></a>Logiska partitioner

En logisk partition består av en uppsättning objekt som har samma partitionsnyckel. I en behållare som innehåller data om livsmedels foder innehåller alla objekt till exempel en `foodGroup` egenskap. Du kan använda `foodGroup` som partitionsnyckel för behållaren. Grupper av objekt som har specifika värden för `foodGroup`, till exempel `Beef Products`,`Baked Products`, och `Sausages and Luncheon Meats`som utgör distinkta logiska partitioner. Du behöver inte oroa dig för att ta bort en logisk partition när underliggande data tas bort.

En logisk partition definierar även omfattningen av databas transaktioner. Du kan uppdatera objekt i en logisk partition med hjälp av en [transaktion med ögonblicks bild isolering](database-transactions-optimistic-concurrency.md). När nya objekt läggs till i en behållare, skapas nya logiska partitioner transparent av systemet.

Det finns ingen gräns för antalet logiska partitioner i din behållare. Varje logisk partition kan lagra upp till 20 GB data. Lämpliga alternativ för partitionsalternativ har ett brett utbud av möjliga värden. I en behållare där alla objekt innehåller en `foodGroup`egenskap kan till exempel data i den `Beef Products` logiska partitionen växa upp till 20 GB. [Genom att välja en partitionsnyckel](partitioning-overview.md#choose-partitionkey) med en mängd olika möjliga värden ser du till att behållaren kan skalas.

## <a name="physical-partitions"></a>Fysiska partitioner

En Azure Cosmos-behållare skalas genom att distribuera data och data flöde över fysiska partitioner. Internt mappas en eller flera logiska partitioner till en enda fysisk partition. De flesta små Cosmos-behållare har flera logiska partitioner men kräver bara en enda fysisk partition. Till skillnad från logiska partitioner är fysiska partitioner en intern implementering av systemet och de hanteras helt av Azure Cosmos DB.

Antalet fysiska partitioner i Cosmos-behållaren är beroende av följande:

- Mängden allokerat data flöde (varje enskild fysisk partition kan tillhandahålla ett data flöde på upp till 10 000 enheter för programbegäran per sekund)
- Total data lagring (varje enskild fysisk partition kan lagra upp till 50 GB)

Det finns ingen gräns för det totala antalet fysiska partitioner i din behållare. När det etablerade data flödet eller data storleken ökar, skapar Azure Cosmos DB automatiskt nya fysiska partitioner genom att dela befintliga. Delning av fysiska partitioner påverkar inte programmets tillgänglighet. Efter delningen av den fysiska partitionen kommer alla data i en enda logisk partition fortfarande att lagras på samma fysiska partition. En fysisk partitions delning skapar helt enkelt en ny mappning av logiska partitioner till fysiska partitioner.

Det data flöde som har allokerats för en behållare är jämnt fördelat mellan fysiska partitioner. En partitionsnyckel som inte distribuerar data flödes förfrågningar jämnt kan skapa "varma" partitioner. Varma partitioner kan leda till hastighets begränsning och ineffektiv användning av det etablerade data flödet och högre kostnader.

Du kan se behållarens fysiska partitioner i **lagrings** avsnittet på **bladet mått** i Azure Portal:

[![Visa antal fysiska partitioner](./media/partition-data/view-partitions-zoomed-out.png)](./media/partition-data/view-partitions-zoomed-in.png#lightbox)

I den här exempel behållaren där vi har `/foodGroup` valt vår partitionsnyckel representerar var och en av de tre rektanglarna en fysisk partition. I avbildningen är **partitionerings nyckel intervallet** detsamma som en fysisk partition. Den valda fysiska partitionen innehåller tre logiska partitioner: `Beef Products`, `Vegetable and Vegetable Products`och `Soups, Sauces, and Gravies`.

Om vi etablerar ett data flöde av 18 000-enheter för programbegäran per sekund (RU/s), kan var och en av de tre fysiska partitionerna använda 1/3 av det totala etablerade data flödet. I den valda fysiska partitionen, kan de logiska `Beef Products`partitionernas `Vegetable and Vegetable Products`nycklar, `Soups, Sauces, and Gravies` och kan gemensamt, använda den fysiska partitionens 6 000 etablerade ru/s. Eftersom det etablerade data flödet är jämnt delat över din behållares fysiska partitioner, är det viktigt att välja en partitionsnyckel som jämnt distribuerar data flödes förbrukningen genom [att välja rätt logisk partitionsnyckel](partitioning-overview.md#choose-partitionkey). Om du väljer en partitionsnyckel som jämnt distribuerar data flödes förbrukningen mellan logiska partitioner, ser du till att data flödes förbrukningen över fysiska partitioner är balanserade.

## <a name="replica-sets"></a>Replik uppsättningar

Varje fysisk partition består av en uppsättning repliker, även kallade en [*replik uppsättning*](global-dist-under-the-hood.md). Varje replik uppsättning är värd för en instans av Azure Cosmos-databasmotorn. En replik uppsättning gör data lagrade i den fysiska partitionen tåligt, hög tillgängliga och konsekvent. Varje replik som utgör den fysiska partitionen ärver partitionens lagrings kvot. Alla repliker av en fysisk partition har gemensamt stöd för det data flöde som har allokerats till den fysiska partitionen. Azure Cosmos DB hanterar automatiskt replik uppsättningar.

De flesta små Cosmos-behållare kräver bara en enda fysisk partition men kommer fortfarande att ha minst 4 repliker.

Följande bild visar hur logiska partitioner mappas till fysiska partitioner som distribueras globalt:

![En bild som visar Azure Cosmos DB partitionering](./media/partition-data/logical-partitions.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [att välja en partitionsnyckel](partitioning-overview.md#choose-partitionkey).
* Lär dig mer om [etablerade data flöden i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
