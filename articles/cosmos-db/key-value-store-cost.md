---
title: Begär enhetsavgifter för Azure Cosmos DB som ett nyckelvärdeslager
description: Lär dig mer om begäranhetsavgifter för Azure Cosmos DB för enkla skriv- och läsåtgärder när den används som en nyckel-/värdebutik.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647511"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB som ett nyckelvärdeslager – kostnadsöversikt

Azure Cosmos DB är en globalt distribuerad databastjänst med flera modeller för att enkelt skapa storskaliga program. Som standard indexerar Azure Cosmos DB automatiskt och effektivt alla data som används. Detta möjliggör snabba och konsekventa [SQL-frågor](how-to-sql-query.md) (och [JavaScript)](stored-procedures-triggers-udfs.md)på data. 

I den här artikeln beskrivs kostnaden för Azure Cosmos DB för enkla skriv- och läsåtgärder när den används som en nyckel-/värdebutik. Skrivåtgärder omfattar infogar, ersätter, tar bort och upserts av dataobjekt. Förutom att garantera ett serviceavtal med 99,999 % tillgänglighet för alla konton med flera regioner erbjuder Azure Cosmos DB garanterad <10 ms latens för läsningar och för (indexerade) skrivningar, vid den 99:e percentilen. 

## <a name="why-we-use-request-units-rus"></a>Varför vi använder Request Units (RUs)

Azure Cosmos DB-prestanda baseras på mängden etablerat dataflöde som uttrycks i [BEGÄRANheter](request-units.md) (RU/s). Avsättningen sker på en andra granularitet och köps i RU/s[(ej att förväxla med timfaktureringen).](https://azure.microsoft.com/pricing/details/cosmos-db/) Ru:er bör betraktas som en logisk abstraktion (en valuta) som förenklar etableringen av obligatoriskt dataflöde för programmet. Användarna behöver inte tänka på att skilja mellan läs- och skrivdataflöde. Den gemensamma valutamodellen för ru:er skapar effektivitetsvinster för att dela den etablerade kapaciteten mellan läsningar och skrivningar. Den här etablerade kapacitetsmodellen gör det möjligt för tjänsten att tillhandahålla ett **förutsägbart och konsekvent dataflöde, garanterad låg latens och hög tillgänglighet**. Slutligen, medan RU-modellen används för att avbilda dataflöde, har varje etablerad RU också en definierad mängd resurser (t.ex. minne, kärnor/CPU och IOPS).

Som ett globalt distribuerat databassystem är Cosmos DB den enda Azure-tjänsten som tillhandahåller omfattande SLA som täcker svarstid, dataflöde, konsekvens och hög tillgänglighet. Det dataflöde som du etablerar tillämpas på var och en av de regioner som är associerade med ditt Cosmos-konto. För läsningar erbjuder Cosmos DB flera, väldefinierade [konsekvensnivåer](consistency-levels.md) som du kan välja mellan. 

I följande tabell visas antalet ru:er som krävs för att utföra läs- och skrivåtgärder baserat på ett dataobjekt med storlek 1 KB och 100 KBs med automatisk standardindexering inaktiverad. 

|Artikelns storlek|1 Läs|1 Skriv|
|-------------|------|-------|
|1 kB|1 RU|5 Ru:er|
|100 kB|10 RU:er|50 Ru:er|

## <a name="cost-of-reads-and-writes"></a>Kostnad för läsningar och skrivningar

Om du avser 1 000 RU/s uppgår detta till 3,6 miljoner RU/timme och kostar 0,08 USD för timmen (i USA och Europa). För en datapost med 1 KB storlek innebär det att du kan använda 3,6 miljoner läsningar eller 0,72 miljoner skrivningar (3,6 miljoner RU / 5) med hjälp av ditt etablerade dataflöde. Normaliserad till miljoner läser och skriver, skulle kostnaden vara $ 0,022 / miljoner läsningar ($ 0,08 / 3,6) och $ 0.111/million av skriver ($ 0,08 / 0,72). Kostnaden per miljon blir minimal som visas i tabellen nedan.

|Artikelns storlek|Kostnaden för 1 miljon läsningar|Kostnad på 1 miljon skriver|
|-------------|-------|--------|
|1 kB|$0.022|$0.111|
|100 kB|$0.222|$1.111 $111 $1.111 $1.|


De flesta av de grundläggande blob eller objekt butiker tjänster ut $ 0,40 per miljon lästransaktion och $ 5 per miljon skriva transaktion. Om den används optimalt kan Cosmos DB vara upp till 98 % billigare än dessa andra lösningar (för 1 KB-transaktioner).

## <a name="next-steps"></a>Nästa steg

* Använd [RU-kalkylatorn](https://cosmos.azure.com/capacitycalculator/) för att uppskatta dataflödet för dina arbetsbelastningar.

