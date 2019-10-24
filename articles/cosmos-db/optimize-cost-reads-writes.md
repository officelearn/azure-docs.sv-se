---
title: Optimera kostnaden för läsningar och skrivningar i Azure Cosmos DB
description: Den här artikeln förklarar hur du minskar Azure Cosmos DB kostnader när du utför Läs-och skriv åtgärder för data.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 934853b80c6e6377923df4c2b5cce7b7d7d57d7c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754933"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optimera läsning och skrivning av kostnader i Azure Cosmos DB

Den här artikeln beskriver hur den kostnad som krävs för att läsa och skriva data från Azure Cosmos DB beräknas. Läs åtgärder omfattar Hämta åtgärder för objekt och skriv åtgärder omfattar Infoga, Ersätt, ta bort och upsert av objekt.  

## <a name="cost-of-reads-and-writes"></a>Kostnader för läsningar och skrivningar

Azure Cosmos DB garanterar förutsägbar prestanda med avseende på data flöde och latens med hjälp av en etablerad data flödes modell. Det data flöde som tillhandahålls är representerat i [enheter för programbegäran](request-units.md) per sekund eller ru/s. En enhet för programbegäran (RU) är en logisk abstraktion över beräknings resurser, till exempel processor, minne, IO, osv. som krävs för att utföra en begäran. Det etablerade data flödet (ru: er) har tagits ur bruk och är dedikerat för din behållare eller databas för att tillhandahålla förutsägbart data flöde och svars tider. Med det etablerade data flödet kan Azure Cosmos DB tillhandahålla förutsägbara och konsekventa prestanda, garanterad låg latens och hög tillgänglighet i valfri skala. Enheter för programbegäran representerar den normaliserade valutan som fören klar orsaken till hur många resurser ett program behöver. 

Du behöver inte tänka på att särskilja enheter för programbegäran mellan läsningar och skrivningar. Den enhetliga valuta modellen för enheter för programbegäran skapar effektivitets vinster för att utbytbara samma data flödes kapacitet för både läsningar och skrivningar. Följande tabell visar kostnaden för läsningar och skrivningar i termer av RU/s för objekt som är 1 KB och 100 KB stora.

|**Objekt storlek**  |**Kostnad för en läsning** |**Kostnad för en skrivning**|
|---------|---------|---------|
|1 KB |1 RU |5 ru: er |
|100 kB |10 RU:er |50 ru: er |

Läsning av ett objekt som är 1 KB i storlek kostar ett RU. Skriver ett objekt som är 1-KB-kostnad fem ru: er. Läs-och skriv kostnaderna gäller när du använder standard nivån för sessionens [konsekvens](consistency-levels.md).  Överväganden kring ru: er omfattar: objekt storlek, antal egenskaper, data konsekvens, indexerade egenskaper, indexering och fråge mönster.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Normaliserad kostnad för 1 000 000 läsningar och skrivningar

Etableringen 1 000 RU/s översätts till 3 600 000 RU/timme och kommer att kosta $0,08 för timmen (i USA och Europa). För ett objekt på 1 KB kan du utföra 3 600 000 läsningar eller 720 000 skrivningar (detta värde beräknas som: `3.6 million RU / 5`) per timme med det etablerade data flödet. Normaliserat till miljon läsningar och skrivningar blir kostnaden $0,022 för 1 000 000 läsningar (detta värde beräknas som: $0,08/3.6 miljoner) och $0,111 för 1 000 000 skrivningar (detta värde beräknas som: $0,08/0,72 miljoner).

## <a name="number-of-regions-and-the-request-units-cost"></a>Antal regioner och kostnader för enheter för begäran

Kostnaden för skrivningar är konstant oavsett hur många regioner som är associerade med Azure Cosmos-kontot. Med andra ord kommer en 1 KB-skrivning att kosta fem ru: er oberoende av antalet regioner som är associerade med kontot. Det finns en icke-trivial mängd resurser som ägnas åt att replikera, acceptera och bearbeta replikeringstrafiken i varje region. Mer information om kostnads optimering i flera regioner finns i artikeln [om att optimera kostnaden för Cosmos-konton med flera regioner](optimize-cost-regions.md) .

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optimera kostnaden för skrivningar och läsningar

När du utför Skriv åtgärder bör du tillhandahålla tillräckligt med kapacitet för att stödja antalet skrivningar som behövs per sekund. Du kan öka det etablerade data flödet med hjälp av SDK, portalen, CLI innan du utför skrivningarna och sedan minska data flödet när skrivningen är slutförd. Data flödet för Skriv perioden är det minsta data flöde som krävs för de data som krävs, plus det data flöde som krävs för att infoga arbets belastningar förutsatt att inga andra arbets belastningar körs. 

Om du kör andra arbets belastningar samtidigt, till exempel fråga/läsa/uppdatera/ta bort, bör du lägga till de ytterligare begär ande enheter som krävs för dessa åtgärder. Om Skriv åtgärderna är avgiftsbelagda kan du anpassa principen för återförsök/backoff genom att använda Azure Cosmos DB SDK: er. Du kan till exempel öka belastningen tills en låg frekvens med begär Anden får en hastighets begränsning. Om Rate-Limit inträffar bör klient programmet stängas av vid Rate-Limiting-begäranden för det angivna intervallet för återförsök. Innan du försöker skriva igen bör du ha en minimal tidsrymd mellan återförsök. Princip support för återförsök ingår i SQL .NET, Java, Node. js och python SDK: er och alla versioner av .NET Core SDK: er som stöds. 

Du kan också lägga till data i Azure Cosmos DB eller kopiera data från ett käll data lager som stöds till Azure Cosmos DB med [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory integreras internt med Azure Cosmos DB bulk API för att ge bästa möjliga prestanda när du skriver data.

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig mer om kostnads optimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om hur [du optimerar kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
