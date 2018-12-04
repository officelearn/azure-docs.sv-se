---
title: Azure Cosmos DB som ett nyckelvärde – kostnaden översikt
description: Läs mer om de låga kostnaderna för att använda Azure Cosmos DB som ett nyckelvärde.
keywords: nyckelvärde
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 7331db23757ab4eaae054c7fe640952fe22a7917
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840885"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB som ett nyckelvärde – kostnaden översikt

Azure Cosmos DB är en globalt distribuerad databastjänst för att bygga högtillgängliga, storskaliga program enkelt. Azure Cosmos DB indexerar automatiskt alla data som den matar in, effektivt som standard. Detta möjliggör snabbt och konsekvent [SQL](how-to-sql-query.md) (och [JavaScript](programming.md)) frågor för alla typer av data. 

Den här artikeln beskriver kostnaden för Azure Cosmos DB för skrivning enkla och läsåtgärder när den används som en nyckel/värde-lager. Skriva-åtgärder inkluderar infogningar, ersätter, borttagningar och upsertar av dokument. Förutom, vilket ger en 99,99% tillgänglighet för alla och alla konton i flera regioner med Avslappnad konsekvens och 99,999% läsningstillgänglighet för alla databaskonton, Azure Cosmos DB erbjuder garanterad < 10 ms svarstid för läser och < 15 ms svarstid för (indexerade), vid skrivning vid 99: e percentilen. 

## <a name="why-we-use-request-units-rus"></a>Varför vi använder programbegäran (ru: er)

Azure Cosmos DB-prestanda är baserad på mängden etablerade [programbegäran](request-units.md) (RU) för partitionen. Etableringen är med en andra Precision och köps i ru/sek ([ska inte förväxlas med fakturering per timme](https://azure.microsoft.com/pricing/details/cosmos-db/)). RU: er bör betraktas som en valuta som förenklar etableringen av nödvändiga genomströmning för programmet. Våra kunder behöver inte tänka på hur man skiljer mellan läsa och skriva kapacitetsenheter. Den enda valuta modellen med enheter för programbegäran skapar effektivitet om du vill dela etablerad kapacitet mellan läsningar och skrivningar. Den här modellen etablerad kapacitet gör att tjänsten en förutsägbar och konsekvent dataflöde garanterad låg latens och hög tillgänglighet. Slutligen kan vi använda RU att modellen dataflöde, men varje etablerade RU har också en definierad mängd resurser (minne, Core). RU/sek är inte bara IOPS.

Cosmos DB är endast Azure-tjänst som tillhandahåller ett serviceavtal på svarstid, dataflöde och enhetlighet förutom hög tillgänglighet som ett globalt distribuerade databassystem. Dataflödet som du etablerar tillämpas på var och en av de regioner som associeras med din Cosmos DB-databaskonto. För läsningar, Cosmos DB erbjuder flera väldefinierade [konsekvensnivåer](consistency-levels.md) där du kan välja från. 

I följande tabell visas antalet mediereserverade enheter som krävs för att utföra läsa och skriva transaktioner på dokumentstorlek av 1 KB och 100 kB-artiklar.

|Objektets storlek|1 Läs|1 skrivning|
|-------------|------|-------|
|1 KB|1 RU|5 ru: er|
|100 KB|10 RU:er|50 ru: er|

## <a name="cost-of-reads-and-writes"></a>Kostnaden för läsningar och skrivningar

Om du etablerar 1 000 kosta RU/sek, den här belopp som ska 3.6 miljoner RU/timme och kommer att $0.08 under timmen (i USA och Europa). Efter 1 KB storlek dokument, detta innebär att du kan använda 3.6-m läsningar eller skriver 0,72-m (3.6 miljoner RU / 5) med hjälp av det etablerade dataflödet. Normaliseras till miljoner läsningar och skrivningar, kostnaden skulle bli $0,022 /m läsningar (0,08 USD / 3.6) och $0.111/ m skriver (0,08 USD / 0,72). Kostnaden per miljon blir minimal som visas i tabellen nedan.

|Objektets storlek|1-m Läs|1-m-Write|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


De flesta av grundläggande blob eller objektet lagrar tjänster kostnad $0,40 per miljon lästransaktioner och 5 USD per miljon skrivning transaktion. Om används optimalt, kan Cosmos DB vara upp till 98% billigare än andra lösningarna (för 1 KB transaktioner).

## <a name="next-steps"></a>Nästa steg

Håll ögonen öppna för nya artiklar om hur du optimerar etablering av Azure Cosmos DB. Under tiden kan passa på att använda vår [RU Kalkylatorn](https://www.documentdb.com/capacityplanner).

