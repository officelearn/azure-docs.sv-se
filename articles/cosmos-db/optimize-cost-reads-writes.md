---
title: Optimera kostnaden för läsningar och skrivningar i Azure Cosmos DB
description: I den här artikeln beskrivs hur du minskar Azure Cosmos DB-kostnader när du utför läs- och skrivåtgärder på data.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 725876594a7e7c5f3b3a02802f487dc5fdfb64dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535943"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optimera läsningar och skrivkostnader i Azure Cosmos DB

I den här artikeln beskrivs hur kostnaden som krävs för att läsa och skriva data från Azure Cosmos DB beräknas. Läsåtgärder omfattar hämta åtgärder för artiklar och skrivåtgärder inkluderar infoga, ersätta, ta bort och upsert av objekt.  

## <a name="cost-of-reads-and-writes"></a>Kostnad för läsningar och skrivningar

Azure Cosmos DB garanterar förutsägbara prestanda när det gäller dataflöde och svarstid med hjälp av en etablerad dataflödesmodell. Den etablerade dataflödet representeras i form av [begärandeenheter](request-units.md) per sekund, eller RU/s. En begäranhet (RU) är en logisk abstraktion över beräkningsresurser som CPU, minne, IO osv. Det etablerade dataflödet (Ru: er) är avsatt och dedikerat till din behållare eller databas för att ge förutsägbart dataflöde och svarstid. Etablerat dataflöde gör det möjligt för Azure Cosmos DB att tillhandahålla förutsägbara och konsekventa prestanda, garanterad låg latens och hög tillgänglighet i valfri skala. Begärandeenheter representerar den normaliserade valutan som förenklar resonemanget om hur många resurser ett program behöver. 

Du behöver inte tänka på att skilja begäran enheter mellan läser och skriver. Den enhetliga valutamodellen för begärandeenheter skapar effektivitetsvinster för att omväxlande använda samma dataflödeskapacitet för både läsningar och skrivningar. I följande tabell visas kostnaden för läsningar och skrivningar i termer av RU/s för artiklar som är 1 KB och 100 kB i storlek.

|**Artikelns storlek**  |**Kostnad för en läsning** |**Kostnad för en skrivning**|
|---------|---------|---------|
|1 kB |1 RU |5 Ru:er |
|100 kB |10 RU:er |50 Ru:er |

Läsa ett objekt som är 1 KB i storlek kostar en RU. Att skriva ett objekt som är 1 KB kostar fem ru: er. Läs- och skrivkostnaderna gäller när [standardperiodens konsekvensnivå](consistency-levels.md)tillämpas .  Övervägandena kring ru:er omfattar: artikelstorlek, egenskapsantal, datakonsekvens, indexerade egenskaper, indexering och frågemönster.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optimera kostnaden för skrivningar och läsningar

När du utför skrivåtgärder bör du etablera tillräckligt med kapacitet för att stödja antalet skrivningar som behövs per sekund. Du kan öka det etablerade dataflödet med hjälp av SDK, portal, CLI innan du utför skrivningar och sedan minska dataflödet när skrivningarna har slutförts. Ditt dataflöde för skrivperioden är det minsta dataflöde som behövs för de angivna data, plus det dataflöde som krävs för att infoga arbetsbelastning förutsatt att inga andra arbetsbelastningar körs. 

Om du kör andra arbetsbelastningar samtidigt, till exempel fråga/läs/uppdatera/ta bort, bör du lägga till ytterligare begäranheter som krävs för dessa åtgärder också. Om skrivåtgärderna är rate-limited kan du anpassa återförsöks-/backoff-principen med hjälp av Azure Cosmos DB SDK:er. Du kan till exempel öka belastningen tills en liten mängd begäranden blir begränsad. Om hastighetsgränsen inträffar ska klientprogrammet backa från på hastighetsbegränsande begäranden för det angivna återförsöksintervallet. Innan du försöker skriva igen bör du ha ett minimalt tidsgap mellan återförsöken. Principstöd för återförsök ingår i SQL .NET, Java, Node.js och Python SDK:er och alla versioner av .NET Core-SDK:erna som stöds. 

Du kan också massinskära data i Azure Cosmos DB eller kopiera data från alla källdatalager som stöds till Azure Cosmos DB med hjälp av [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory integreras inbyggt med Azure Cosmos DB Bulk API för att ge bästa prestanda när du skriver data.

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta att lära dig mer om kostnadsoptimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att förstå din Azure Cosmos DB-faktura](understand-your-bill.md)
* Läs mer om [att optimera kostnaden för dataflöde](optimize-cost-throughput.md)
* Läs mer om [att optimera lagringskostnaden](optimize-cost-storage.md)
* Läs mer om [att optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [att optimera kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
