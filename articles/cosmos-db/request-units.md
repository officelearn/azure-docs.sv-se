---
title: Enheter för programbegäran och data flöde i Azure Cosmos DB
description: Läs om hur du anger och beräknar krav för begär ande enheter i Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6e5d95a47261445e3031f55368f4e2cd8e2830a7
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754855"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

Med Azure Cosmos DB betalar du för det data flöde som du tillhandahåller och den lagring du förbrukar per timme. Data flödet måste tillhandahållas för att säkerställa att tillräckligt med system resurser är tillgängliga för din Azure Cosmos-databas hela tiden. Du behöver tillräckligt med resurser för att möta eller överskrida [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har en egen uppsättning databas åtgärder. Dessa åtgärder sträcker sig från enkla punkter som läser och skriver till komplexa frågor. Varje databas åtgärd förbrukar system resurser baserat på åtgärdens komplexitet. 

Kostnaden för alla databas åtgärder normaliseras av Azure Cosmos DB och uttrycks av *enheter för programbegäran* (eller ru: er, för kort). Du kan tänka dig ru: er per sekund som valuta för data flödet. Ru: er per sekund är en Rate-baserad valuta. Den sammanfattar de system resurser som CPU, IOPS och minne som krävs för att utföra de databas åtgärder som stöds av Azure Cosmos DB. 

Kostnaden för att läsa en 1 KB-artikel är 1 begär ande enhet (eller 1 RU). Alla andra databas åtgärder tilldelas samma kostnad med ru: er. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare, mäts kostnader alltid med ru: er. Om databas åtgärden är en Skriv-, Läs-eller-fråga, mäts kostnader alltid i ru: er.

Följande bild visar en övergripande idé på ru: er:

![Databas åtgärder förbrukar enheter för programbegäran](./media/request-units/request-units.png)

Azure Cosmos DB säkerställer att antalet ru: er för en specifik databas åtgärd över en specifik data uppsättning är deterministisk för att hantera och planera kapacitet. Du kan granska svars huvudet för att spåra antalet ru: er som används av en databas åtgärd. När du förstår de [faktorer som påverkar ru-avgifterna](request-units.md#request-unit-considerations) och ditt programs data flödes krav kan du effektivt köra program kostnaden.

Du etablerar antalet ru: er för ditt program per sekund i steg om 100 ru: er per sekund. Om du vill skala det etablerade data flödet för ditt program kan du när som helst öka eller minska antalet ru: er. Du kan skala i steg om eller minskar 100 ru: er. Du kan göra dina ändringar antingen via programmering eller med hjälp av Azure Portal. Du faktureras per timme.

Du kan etablera data flöde med två olika granularitet: 

* **Behållare**: Mer information finns i [etablera data flöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* **Databaser**: Mer information finns i [etablera data flöde på en Azure Cosmos-databas](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Begär enhets överväganden

När du uppskattar antalet ru: er per sekund som ska etableras, bör du tänka på följande faktorer:

* **Objekt storlek**: när ett objekts storlek ökar ökar också antalet ru: er som förbrukas för att läsa eller skriva objektet.

* **Objekt indexering**: som standard indexeras varje objekt automatiskt. Färre ru: er förbrukas om du väljer att inte indexera några av dina objekt i en behållare.

* **Antal objekt egenskaper**: förutsatt att standard indexeringen är på alla egenskaper ökar antalet ru: er som används för att skriva ett objekt när antalet objekt egenskaper ökar.

* **Indexerade egenskaper**: en index princip för varje behållare avgör vilka egenskaper som är indexerade som standard. Begränsa antalet indexerade egenskaper för att minska RU-förbrukningen för Skriv åtgärder.

* **Data konsekvens**: de starka och gränsade inaktuella konsekvens nivåerna förbrukar ungefär två gånger mer ru: er medan Läs åtgärder utförs jämfört med andra avslappnade konsekvens nivåer.

* **Frågeuttryck**: en frågas komplexitet påverkar hur många ru: er som används för en åtgärd. Faktorer som påverkar kostnaden för Query-åtgärder är: 
    
    - Antalet frågeresultat
    - Antalet predikat
    - Typen av predikat
    - Antalet användardefinierade funktioner
    - Storleken på käll informationen
    - Resultat uppsättningens storlek
    - Projektioner

  Azure Cosmos DB garanterar att samma fråga på samma data alltid kostar samma antal ru: er vid upprepade körningar.

* **Skript användning**: som med frågor, lagrade procedurer och utlösare använder ru: er baserat på komplexiteten för de åtgärder som utförs. När du utvecklar ditt program bör du kontrol lera [rubriken begär ande avgift](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) för att bättre förstå hur mycket ru-kapacitet varje åtgärd förbrukar.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du [etablerar data flöde i Azure Cosmos-behållare och databaser](set-throughput.md).
* Läs mer om [logiska partitioner](partition-data.md).
* Lär dig mer om hur du [globalt skalar allokerat data flöde](scaling-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
* Lär dig hur du [hittar enhets avgiften för en åtgärd](find-request-unit-charge.md).
* Lär dig hur du [optimerar den etablerade data flödes kostnaden i Azure Cosmos DB](optimize-cost-throughput.md).
* Lär dig hur du [optimerar läsning och skrivning av kostnader i Azure Cosmos DB](optimize-cost-reads-writes.md).
* Lär dig hur du [optimerar kostnad för frågor i Azure Cosmos DB](optimize-cost-queries.md).
* Lär dig hur du [använder mått för att övervaka data flödet](use-metrics.md).
