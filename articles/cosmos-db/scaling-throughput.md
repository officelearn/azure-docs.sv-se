---
title: Skalning av dataflöden i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB kan skalas dataflöde Elastiskt
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 5834bf88b63209d2374cfea71fc0f01a1f7451c5
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033442"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>Skalning av dataflöden i Azure Cosmos DB

I Azure Cosmos DB, etablerat dataflöde representeras som begäran begärandeenheter/sekund (RU/s, plural: RU: er). RU: er mäta kostnaden för både läs- och skrivåtgärder mot din Cosmos-behållare som du ser i följande bild:

![Begäransenheter](./media/scale-throughput/figure1.png)

Du kan etablera ru: er i en Cosmos-behållare eller en Cosmos-databas. RU som etableras i en behållare är endast tillgängligt för åtgärder som utförs på den behållaren. RU som etableras för en databas delas mellan alla behållare i databasen (förutom för alla behållare som exklusivt tilldelade ru: er).

För att skala Elastiskt dataflöde, kan du öka eller minska etablerade RU/s när som helst. Mer information finns i [How-to etablera dataflöde](set-throughput.md) och Elastiskt skala Cosmos-behållare och databaser. Globalt skalning av dataflöde, kan du lägga till eller ta bort regioner på ditt Cosmos-konto när som helst. Mer information finns i [Lägg till/ta bort regioner från ditt databaskonto](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Associera flera regioner med ett Cosmos-konto är viktigt i många scenarier för att uppnå med låg latens och [hög tillgänglighet](high-availability.md) runt om i världen.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>hur etablerade dataflödet som distribueras i flera regioner

Om du etablerar ”R” ru: er på en Cosmos-behållare (eller databas), Cosmos DB garanterar att ”R” ru: er är tillgängliga i *varje* region som är associerade med ditt Cosmos-konto. Varje gång du lägger till en ny region till ditt konto, etablerar Cosmos DB automatiskt ”R” ru: er i regionen nyligen tillagda. De åtgärder som utförs mot din Cosmos-behållare garanteras att hämta ”R” ru: er i varje region. Du kan inte selektivt tilldela ru: er i en specifik region. RU som etableras för en Cosmos-behållare (eller databas) har etablerats för alla regioner som är associerat med ditt Cosmos-konto.

Förutsatt att en Cosmos-behållare har konfigurerats med ”R” ru: er och det är ”n” regioner som är associerade med kontot Cosmos sedan:

- Om Cosmos-konto har konfigurerats med en enda skrivregionen, totalt antal ru globalt på behållaren = R x N.

- Om Cosmos-konto har konfigurerats med flera Skriv-regioner, totalt antal ru globalt på behållaren = R x (N + 1). RU: er för ytterligare R etableras automatiskt att processen Uppdateringskonflikter och anti entropi trafik över regionerna.

Ditt val av [konsekvensmodell](consistency-levels.md) påverkar också dataflöde. Du kan hämta cirka 2 x läsningsgenomströmning för sessionen, konsekvent prefix och slutlig konsekvens jämfört med bunden föråldring eller stark konsekvens.

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig hur du konfigurerar dataflöde med hjälp av följande artikel:

* [Hämta och ange dataflöde för behållare och databaser](set-throughput.md) 

