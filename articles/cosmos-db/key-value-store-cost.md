---
title: Begär units för Azure Cosmos DB som ett nyckelvärde
description: Lär dig mer om begäran units i Azure Cosmos DB för skrivning enkla och läsåtgärder när den används som en nyckel/värde-lager.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3a79db11ff05bcc9d18619c7f508a9864c17c3b8
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012798"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB som ett nyckel värdes lager – kostnads översikt

Azure Cosmos DB är en globalt distribuerad databastjänst för att bygga högtillgängliga, storskaliga program enkelt. Som standard indexeras Azure Cosmos DB automatiskt och effektivt indexera alla data som matas in. Detta möjliggör snabba och konsekventa [SQL](how-to-sql-query.md) -frågor (och [Java Script](stored-procedures-triggers-udfs.md)) för data. 

Den här artikeln beskriver kostnaden för Azure Cosmos DB för skrivning enkla och läsåtgärder när den används som en nyckel/värde-lager. Skriv åtgärder inkluderar infogningar, ersätter, tar bort och upsertar av data objekt. Förutom att garantera ett service avtal på 99,999% tillgänglighet för alla konton med flera regioner, Azure Cosmos DB ger garanterad < 10 MS-svars tid för läsningar och för (indexerade) skrivningar, vid 99 percentil. 

## <a name="why-we-use-request-units-rus"></a>Varför vi använder programbegäran (ru: er)

Azure Cosmos DB prestanda baseras på mängden allokerat data flöde som uttrycks i [enheter](request-units.md) för programbegäran (ru/s). Etableringen är i en andra kornig het och köps in i RU/s (ska[inte förväxlas med fakturerings tiden per timme](https://azure.microsoft.com/pricing/details/cosmos-db/)). Ru: er bör betraktas som en logisk abstraktion (en valuta) som fören klar etableringen av nödvändigt data flöde för programmet. Användarna behöver inte tänka på att skilja mellan Läs-och Skriv data flöden. Den enda valuta modellen med enheter för programbegäran skapar effektivitet om du vill dela etablerad kapacitet mellan läsningar och skrivningar. Den här etablerade kapacitets modellen gör det möjligt för tjänsten att tillhandahålla ett **förutsägbart och konsekvent data flöde, garanterad låg latens och hög tillgänglighet**. Slutligen, medan RU-modellen används för att illustrera data flödet, har varje etablerad RU också en definierad mängd resurser (t. ex. minne, kärnor/processor och IOPS).

Som ett globalt distribuerat databas system är Cosmos DB den enda Azure-tjänst som tillhandahåller en omfattande service avtal som täcker svars tid, data flöde, konsekvens och hög tillgänglighet. Det data flöde som du etablerar tillämpas på var och en av de regioner som är kopplade till ditt Cosmos-konto. För läsningar, Cosmos DB erbjuder flera väldefinierade [konsekvensnivåer](consistency-levels.md) där du kan välja från. 

I följande tabell visas antalet ru: er som krävs för att utföra Läs-och skriv åtgärder baserat på ett data objekt av storlek 1 KB och 100 kB.

|Objektets storlek|1 Läs|1 skrivning|
|-------------|------|-------|
|1 KB|1 RU|5 ru: er|
|100 KB|10 RU:er|50 ru: er|

## <a name="cost-of-reads-and-writes"></a>Kostnaden för läsningar och skrivningar

Om du etablerar 1 000 RU/s uppgår detta belopp till 3 600 000 RU/timme och kommer att kosta $0,08 för timmen (i USA och Europa). För ett data objekt på 1 KB innebär det att du kan använda 3 600 000 läspaket eller 720 000 skrivningar (3 600 000 RU/5) med hjälp av det etablerade data flödet. Normaliserat till miljon läsningar och skrivningar skulle kostnaden bli $0,022/miljon av läsningar ($0,08/3,6) och $0.111/miljoner skrivningar ($0,08/0,72). Kostnaden per miljon blir minimal som visas i tabellen nedan.

|Objektets storlek|Kostnad för 1 000 000 läsningar|Kostnad på 1 000 000 skrivningar|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


De flesta av grundläggande blob eller objektet lagrar tjänster kostnad $0,40 per miljon lästransaktioner och 5 USD per miljon skrivning transaktion. Om det används optimalt kan Cosmos DB vara upp till 98% billigare än dessa andra lösningar (för 1 KB-transaktioner).

## <a name="next-steps"></a>Nästa steg

* Använd [ru](https://cosmos.azure.com/capacitycalculator/) -Kalkylatorn för att beräkna data flödet för dina arbets belastningar.

