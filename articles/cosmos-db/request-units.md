---
title: Enheter för programbegäran och data flöde i Azure Cosmos DB
description: Läs om hur du anger och beräknar krav för begär ande enheter i Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 3378b442c80758c4a5dca02b33f92ba3823f33ed
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2020
ms.locfileid: "85113659"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

Med Azure Cosmos DB betalar du för det dataflöde du tillhandahåller och den lagring som du för brukar varje timme. Dataflöde måste tillhandahållas för att se till att det alltid finns tillräckligt med tillgängliga systemresurser för din Azure Cosmos-databas. Du behöver tillräckligt med resurser för att möta eller överskrida [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har en egen uppsättning databas åtgärder. Dessa åtgärder sträcker sig från enkla punkter som läser och skriver till komplexa frågor. Varje databas åtgärd förbrukar system resurser baserat på åtgärdens komplexitet. 

Kostnaden för alla databas åtgärder normaliseras av Azure Cosmos DB och uttrycks av *enheter för programbegäran* (eller ru: er, för kort). Du kan tänka på RU:er per sekund som en valuta för dataflöde. RU:er per sekund är en hastighetsbaserad valuta. Den abstraherar systemresurser som CPU, IOPS och minne som krävs för att utföra de databasåtgärder som stöds av Azure Cosmos DB. 

Kostnaden för att läsa ett objekt på 1 KB är 1 enhet för programbegäran (eller 1 RU). Minst 10 RU/s krävs för att lagra varje 1 GB data. Alla andra databasåtgärder tillskrivs på samma sätt en kostnad i form av RU:er. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare mäts kostnaderna alltid med RU:er. Oavsett om databasåtgärden är en läsning, skrivning eller fråga mäts kostnaderna alltid i RU:er.

Följande bild illustrerar den övergripande tanken med RU:er:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Databas åtgärder förbrukar enheter för programbegäran" border="false":::

För att hantera och planera kapacitet ser Azure Cosmos DB till att antalet RU:er för en given databasåtgärd som avser en given datamängd är deterministisk. Du kan undersöka svarshuvudet för att ta reda på antalet RU:er som förbrukas av en valfri databasåtgärd. När du förstår de [faktorer som påverkar ru-avgifterna](request-units.md#request-unit-considerations) och ditt programs data flödes krav kan du effektivt köra program kostnaden.

Du anger antalet RU:er för ditt program per sekund, i steg om 100 RU:er per sekund. Om du vill skala det etablerade dataflödet för ditt program kan du när som helst öka eller minska antalet RU:er. Du kan öka eller minska i steg om 100 RU:er. Du kan utföra dina ändringar programmatiskt eller med hjälp av Azure-portalen. Du debiteras per timme.

Du kan etablera data flöde med två olika granularitet: 

* **Behållare**: Mer information finns i [etablera data flöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* **Databaser**: Mer information finns i [etablera data flöde på en Azure Cosmos-databas](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Överväganden för enhet för programbegäran

När du beräknar hur många enheter för programbegäran som ska tillhandahållas per sekund bör du överväga följande:

* **Objekt storlek**: när ett objekts storlek ökar ökar också antalet ru: er som förbrukas för att läsa eller skriva objektet.

* **Objekt indexering**: som standard indexeras varje objekt automatiskt. Färre enheter för programbegäran används om du väljer att inte indexera vissa av objekten i en container.

* **Antal objekt egenskaper**: förutsatt att standard indexeringen är på alla egenskaper ökar antalet ru: er som används för att skriva ett objekt när antalet objekt egenskaper ökar.

* **Indexerade egenskaper**: en index princip för varje behållare avgör vilka egenskaper som är indexerade som standard. Om du vill minska förbrukningen av enheter för programbegäran för skrivåtgärder bör du begränsa antalet indexerade egenskaper.

* **Data konsekvens**: de starka och gränsade inaktuella konsekvens nivåerna förbrukar ungefär två gånger mer ru: er medan Läs åtgärder utförs jämfört med andra avslappnade konsekvens nivåer.

* **Frågeuttryck**: en frågas komplexitet påverkar hur många ru: er som används för en åtgärd. Faktorer som påverkar kostnaden för frågeåtgärder omfattar: 
    
    - Antalet frågeresultat
    - Antalet predikat
    - Predikatens karaktär
    - Antalet användardefinierade funktioner
    - Storleken på källdata
    - Storleken på resultatuppsättningen
    - Projektioner

  Azure Cosmos DB garanterar att samma fråga på samma data alltid kostar samma antal enheter för programbegäran vid upprepade körningar.

* **Skript användning**: som med frågor, lagrade procedurer och utlösare använder ru: er baserat på komplexiteten för de åtgärder som utförs. När du utvecklar ditt program kan du läsa [rubriken för begärandekostnad](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) för att få mer information om hur mycket RU-kapacitet varje åtgärd förbrukar.

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
