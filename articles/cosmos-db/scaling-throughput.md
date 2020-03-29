---
title: Skalning av dataflöde i Azure Cosmos DB
description: I den här artikeln beskrivs hur Azure Cosmos DB skalar dataflödet i olika regioner där Azure Cosmos-kontot etableras.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873513"
---
# <a name="globally-scale-provisioned-throughput"></a>Skala etablerat dataflöde globalt 

I Azure Cosmos DB representeras etablerat dataflöde som begäranheter/sekund (RU/s eller pluralformulärets ru:er). Ru:er mäter kostnaden för både läs- och skrivåtgärder mot Cosmos-behållaren enligt följande bild:

![Enheter för programbegäran](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Du kan etablera ru:er på en Cosmos-behållare eller en Cosmos-databas. Ru:er som etablerats på en behållare är endast tillgängliga för de åtgärder som utförs på den behållaren. Ru:er som etablerats i en databas delas mellan alla behållare i databasen (förutom behållare med exklusivt tilldelade ru:er).

För elastiskt skalning etablerat dataflöde kan du när som helst öka eller minska de etablerade RU/s. Mer information finns i [Instruktioner för etableringsdataflöde](set-throughput.md) och elastiskt skala Cosmos-behållare och databaser. För globalt skalningsdataflöde kan du när som helst lägga till eller ta bort regioner från ditt Cosmos-konto. Mer information finns i [Lägga till/ta bort regioner från databaskontot](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Att associera flera regioner med ett Cosmos-konto är viktigt i många scenarier – för att uppnå låg latens och [hög tillgänglighet](high-availability.md) runt om i världen.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Så här distribueras etablerat dataflöde mellan regioner

Om du etablerar R-ru:er på en Cosmos-behållare (eller databas) säkerställer Cosmos DB att *R-ru:er* är tillgängliga i *varje* region som är associerad med ditt Cosmos-konto. *'R'* Varje gång du lägger till en ny region *'R'* i ditt konto etablerar Cosmos DB automatiskt R-ru:er i den nyligen tillagda regionen. De åtgärder som utförs mot cosmos-behållaren är garanterade att få *R-ru:er* i varje region. Du kan inte selektivt tilldela ru:er till en viss region. Ru:erna som etablerats på en Cosmos-behållare (eller databas) etableras i alla regioner som är associerade med ditt Cosmos-konto.

Förutsatt att en Cosmos-behållare *'R'* är konfigurerad med R-ru:er och att det finns *N-regioner* som är associerade med Cosmos-kontot, sedan:

- Om Cosmos-kontot är konfigurerat med en enda skrivregion, är det totala ru:erna som är tillgängligt globalt på behållaren = *R* x *N*.

- Om Cosmos-kontot är konfigurerat med flera skrivregioner är det totala ru:er som är tillgängliga globalt i behållaren = *R* x (*N*+1). De *R* ytterligare ru:erna etableras automatiskt för att bearbeta uppdateringskonflikter och anti-entropytrafik i regionerna.

Ditt val av [konsekvensmodell](consistency-levels.md) påverkar också dataflödet. Du kan få ungefär 2x avläsningsgenomströmning för de mer avslappnade konsekvensnivåerna (t.ex. *session,* *konsekvent prefix* och *eventuell* konsekvens) jämfört med starkare konsekvensnivåer (t.ex. *begränsad föråldring* eller *stark* konsekvens).

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig hur du konfigurerar dataflöde på en behållare eller databas:

* [Hämta och ange dataflöde för behållare och databaser](set-throughput.md) 

