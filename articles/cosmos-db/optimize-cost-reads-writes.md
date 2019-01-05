---
title: Optimera kostnaden för läsningar och skrivningar i Azure Cosmos DB
description: Den här artikeln förklarar hur du minska kostnaderna för Azure Cosmos DB när du utför Läs- och skrivåtgärder på data.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: c89374919087dc3bca435b8ce4af2dedd26ccb23
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041330"
---
# <a name="optimize-the-cost-required-to-read-and-write-data-from-azure-cosmos-db"></a>Optimera kostnader som krävs för att läsa och skriva data från Azure Cosmos DB

Den här artikeln beskrivs hur beräknas kostnaden som krävs för att läsa och skriva data från Azure Cosmos DB. Innehåller get-åtgärder på objekt för läsåtgärder och skrivåtgärder omfattar infoga, Ersätt, ta bort och upsert av objekt.  

## <a name="cost-of-reads-and-writes"></a>Kostnaden för läsningar och skrivningar

Azure Cosmos DB garanterar förutsägbar prestanda när det gäller dataflöde och svarstid med hjälp av en modell för etablerat dataflöde. Det tillhandahållna dataflödet representeras i [programbegäran](request-units.md) per sekund eller RU/s. En begäran enhet (RU) är en logisk abstraktion över beräkningsresurser som CPU, minne och IO, etc. som krävs för att utföra en begäran. Dataflöde (ru) noterades och dedikerade till dina behållare eller database för att tillhandahålla förutsägbart dataflöde och svarstid. Etablerat dataflöde kan Azure Cosmos DB att tillhandahålla förutsägbar och konsekvent prestanda, garanterad låg latens och hög tillgänglighet i valfri skala. Begäransenheter representerar den normaliserade valutan som förenklar resonemang om hur många resurser som ett program behöver. 

Du behöver tänka på särskiljer begäransenheter mellan läsningar och skrivningar. Enhetlig valuta modellen av frågeenheter skapar effektivitet för att antingen använda samma genomflödeskapaciteten för både läsningar och skrivningar. I följande tabell visas kostnaden för läsningar och skriver när det gäller RU/s för objekt som är 1 KB och 100 KB i storlek.

|**Objektets storlek**  |**Kostnaden för en Läs** |**Kostnaden för en skrivning**|
|---------|---------|---------|
|1 KB |1 RU |5 ru: er |
|100 KB |10 RU:er |50 ru: er |

Läser ett objekt som är 1 KB storlek kostnader en RU. Skriver ett objekt som är 1 KB kostar fem ru: er. Kostnader för läsning och skrivning gäller när du använder standard-sessionen [konsekvensnivå](consistency-levels.md).  Överväganden kring ru: er omfattar: objektet storlek, antal egenskaper, datakonsekvens, indexerade egenskaper, indexering, och fråga efter mönster.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Normaliserade kostnaden för 1 miljon läsningar och skrivningar

Etablerar 1 000 RU/s översätts till 3.6 miljoner RU/timme och kostar $0.08 under timmen (i USA och Europa). För en 1 KB-artikel, du kan utföra 3.6 miljoner läsningar eller 0,72 miljoner skrivningar (det här värdet beräknas som: `3.6 million RU / 5`) per timme med den här etablerat dataflöde. Normaliseras till en miljoner läsningar och skrivningar, kostnaden skulle bli $0,022 för 1 miljon läsningar (det här värdet beräknas som: $0.08/3.6 miljoner) och $0.111 för 1 miljon skrivningar (det här värdet beräknas som: $0.08/0.72 miljoner).

## <a name="number-of-regions-and-the-request-units-cost"></a>Antalet regioner och begäransenheter kostnad

Kostnaden för skrivningar är konstant oavsett hur många regioner som associeras med Azure Cosmos-konto. Med andra ord kostar en 1 KB-skrivning fem enheter som är oberoende av antalet regioner som är associerade med kontot. Det finns en icke-trivialt mängd resurser i replikering, godkänna och bearbeta replikeringstrafiken på varje region. Mer information om flera regioner kostnad optimering finns [optimera kostnaden för flera regioner Cosmos-konton](optimize-cost-regions.md) artikeln.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optimera kostnaden för skrivningar och läsningar

När du utför skrivåtgärder, bör du tillhandahålla tillräcklig kapacitet för antal skrivningar som behövs per sekund. Du kan öka dataflöden med hjälp av SDK, portalen, CLI innan du utför skrivningar och sedan minska dataflödet när hämtas har slutförts. Ditt dataflöde för skrivperioden är det lägsta dataflöde som krävs för den angivna data plus dataflödet som krävs för att infoga arbetsbelastning förutsatt att några andra arbetsbelastningar som körs. 

Om du kör andra arbetsbelastningar samtidigt, till exempel fråga/Läs/Uppdatera/ta bort, du bör lägga till ytterligare begäransenheter som krävs för dessa åtgärder för. Om skrivåtgärder rate-limited, kan du anpassa återförsök/backoff-princip med hjälp av Azure Cosmos DB SDK: er. Du kan till exempel öka belastningen tills en liten andel begäranden hämtar rate-limited. Om hastighetsbegränsning inträffar klientprogrammet säkerhetskopiera på hastighetsbegränsande begäranden för det angivna återförsöksintervallet. Innan du försöker skrivningar, bör du ha en minimal mängd lucka i tiden mellan försöken. Stöd för återförsök Grupprincip ingår i SQL .NET, Java, Node.js och Python SDK: er och alla versioner av .NET Core-SDK: er som stöds. 

Du kan också massinfoga data till Azure Cosmos DB eller kopiera data från alla dataarkiv till Azure Cosmos DB med hjälp av [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory integreras har inbyggt med Azure Cosmos DB Bulk API att tillhandahålla bästa prestanda vid skrivning av data.

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta till mer information om kostnadsoptimeringar i Azure Cosmos DB i följande artiklar:

* Läs mer om [Optimera för utveckling och testning](optimize-dev-test.md)
* Läs mer om [förstå din faktura för Azure Cosmos DB](understand-your-bill.md)
* Läs mer om [optimera dataflödet kostnad](optimize-cost-throughput.md)
* Läs mer om [optimera kostnaden för lagring](optimize-cost-storage.md)
* Läs mer om [optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [optimera kostnaden för Azure Cosmos-konton för flera regioner](optimize-cost-regions.md)
