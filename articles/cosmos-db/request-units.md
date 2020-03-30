---
title: Begär enheter och dataflöde i Azure Cosmos DB
description: Lär dig mer om hur du anger och beräknar krav på begäransenhet i Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246596"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

Med Azure Cosmos DB betalar du för det dataflöde du tillhandahåller och den lagring som du för brukar varje timme. Dataflöde måste tillhandahållas för att se till att det alltid finns tillräckligt med tillgängliga systemresurser för din Azure Cosmos-databas. Du behöver tillräckligt med resurser för att uppfylla eller [överskrida Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB stöder många API:er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har sin egen uppsättning databasåtgärder. Dessa åtgärder sträcker sig från enkla punktläsar och skrivningar till komplexa frågor. Varje databasåtgärd förbrukar systemresurser baserat på åtgärdens komplexitet. 

Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks av Request Units (eller RU: *er,* för korta). Du kan tänka på RU:er per sekund som en valuta för dataflöde. RU:er per sekund är en hastighetsbaserad valuta. Den abstraherar systemresurser som CPU, IOPS och minne som krävs för att utföra de databasåtgärder som stöds av Azure Cosmos DB. 

Kostnaden för att läsa ett objekt på 1 KB är 1 enhet för programbegäran (eller 1 RU). Minst 10 RU/s krävs för att lagra varje 1 GB data. Alla andra databasåtgärder tillskrivs på samma sätt en kostnad i form av RU:er. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare mäts kostnaderna alltid med RU:er. Oavsett om databasåtgärden är en läsning, skrivning eller fråga mäts kostnaderna alltid i RU:er.

Följande bild illustrerar den övergripande tanken med RU:er:

![Databasåtgärder förbrukar begärandeenheter](./media/request-units/request-units.png)

För att hantera och planera kapacitet ser Azure Cosmos DB till att antalet RU:er för en given databasåtgärd som avser en given datamängd är deterministisk. Du kan undersöka svarshuvudet för att ta reda på antalet RU:er som förbrukas av en valfri databasåtgärd. När du förstår de [faktorer som påverkar RU-avgifter](request-units.md#request-unit-considerations) och programmets dataflödeskrav kan du köra programmet kostnadseffektivt.

Du anger antalet RU:er för ditt program per sekund, i steg om 100 RU:er per sekund. Om du vill skala det etablerade dataflödet för ditt program kan du när som helst öka eller minska antalet RU:er. Du kan öka eller minska i steg om 100 RU:er. Du kan utföra dina ändringar programmatiskt eller med hjälp av Azure-portalen. Du debiteras per timme.

Du kan etablera dataflöde på två olika granulariteter: 

* **Behållare**: Mer information finns i [Etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* **Databaser**: Mer information finns i [Etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Överväganden för enhet för programbegäran

När du beräknar hur många enheter för programbegäran som ska tillhandahållas per sekund bör du överväga följande:

* **Artikelstorlek**: När storleken på ett objekt ökar ökar också antalet ru:er som förbrukas för att läsa eller skriva objektet.

* **Artikelindexering**: Som standard indexeras varje objekt automatiskt. Färre enheter för programbegäran används om du väljer att inte indexera vissa av objekten i en container.

* **Antal objektegenskaper:** Förutsatt att standardindexeringen finns på alla egenskaper ökar antalet ru:er som förbrukas för att skriva ett objekt när antalet objektegenskaper ökar.

* **Indexerade egenskaper**: En indexprincip för varje behållare avgör vilka egenskaper som indexeras som standard. Om du vill minska förbrukningen av enheter för programbegäran för skrivåtgärder bör du begränsa antalet indexerade egenskaper.

* **Datakonsekvens**: De starka och avgränsade konsekvensnivåerna för inaktuellhet förbrukar ungefär två gånger fler ru:er när de utför läsåtgärder jämfört med andra avslappnade konsekvensnivåer.

* **Frågemönster**: Komplexiteten i en fråga påverkar hur många ru:er som förbrukas för en åtgärd. Faktorer som påverkar kostnaden för frågeåtgärder omfattar: 
    
    - Antalet frågeresultat
    - Antalet predikat
    - Predikatens karaktär
    - Antalet användardefinierade funktioner
    - Storleken på källdata
    - Storleken på resultatuppsättningen
    - Projektioner

  Azure Cosmos DB garanterar att samma fråga på samma data alltid kostar samma antal enheter för programbegäran vid upprepade körningar.

* **Skriptanvändning**: Som med frågor förbrukar lagrade procedurer och utlösare ru:er baserat på komplexiteten i de åtgärder som utförs. När du utvecklar ditt program kan du läsa [rubriken för begärandekostnad](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) för att få mer information om hur mycket RU-kapacitet varje åtgärd förbrukar.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur [du etablerar dataflöde på Azure Cosmos-behållare och databaser](set-throughput.md).
* Läs mer om [logiska partitioner](partition-data.md).
* Läs mer om hur du [globalt kan skala etablerat dataflöde](scaling-throughput.md).
* Lär dig hur du [etablerar dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
* Läs om hur du [hittar enhetsavgiften för en åtgärd](find-request-unit-charge.md).
* Lär dig hur du [optimerar etableringskostnaden för dataflöde i Azure Cosmos DB](optimize-cost-throughput.md).
* Lär dig hur du [optimerar läs- och skrivkostnader i Azure Cosmos DB](optimize-cost-reads-writes.md).
* Lär dig hur du [optimerar frågekostnaden i Azure Cosmos DB](optimize-cost-queries.md).
* Lär dig hur du [använder mått för att övervaka dataflödet](use-metrics.md).
