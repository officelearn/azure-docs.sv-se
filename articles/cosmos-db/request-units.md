---
title: Enheter för programbegäran som data flödes-och prestanda valuta i Azure Cosmos DB
description: Läs om hur du anger och beräknar krav för begär ande enheter i Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88604838"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och Table. Varje API har en egen uppsättning databas åtgärder. Dessa åtgärder sträcker sig från enkla punkter som läser och skriver till komplexa frågor. Varje databas åtgärd förbrukar system resurser baserat på åtgärdens komplexitet.

Kostnaden för alla databas åtgärder normaliseras av Azure Cosmos DB och uttrycks av *enheter för programbegäran* (eller ru: er, för kort). Du kan tänka dig ru: er som en prestanda valuta som sammanfattar system resurserna, till exempel CPU, IOPS och minne som krävs för att utföra de databas åtgärder som stöds av Azure Cosmos DB.

Kostnaden för att läsa en punkt (dvs. att hämta ett enskilt objekt med dess ID och partitionsnyckel) för ett 1 KB-objekt är 1 begär ande enhet (eller 1 RU). Alla andra databasåtgärder tillskrivs på samma sätt en kostnad i form av RU:er. Oavsett vilket API du använder för att interagera med din Azure Cosmos-behållare mäts kostnaderna alltid med RU:er. Om databas åtgärden är en skrivning, en läsning eller fråga, mäts kostnader alltid i ru: er.

Följande bild illustrerar den övergripande tanken med RU:er:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Databas åtgärder förbrukar enheter för programbegäran" border="false":::

För att hantera och planera kapacitet ser Azure Cosmos DB till att antalet RU:er för en given databasåtgärd som avser en given datamängd är deterministisk. Du kan undersöka svarshuvudet för att ta reda på antalet RU:er som förbrukas av en valfri databasåtgärd. När du förstår de [faktorer som påverkar ru-avgifterna](request-units.md#request-unit-considerations) och ditt programs data flödes krav kan du effektivt köra program kostnaden.

Vilken typ av Azure Cosmos-konto du använder avgör hur förbrukade ru: er blir debiterade:

- I [etablerat data flödes](set-throughput.md) läge etablerar du antalet ru: er för ditt program per sekund i steg om 100 ru: er per sekund. Om du vill skala det etablerade data flödet för ditt program kan du när som helst öka eller minska antalet ru: er i steg om eller minskningar av 100 ru: er. Du kan göra ändringarna med hjälp av programmering eller via Azure-portalen. Du debiteras per timme för den mängd ru: er per sekund som du har etablerad. Du kan etablera data flöde med två olika granularitet:
  - **Behållare**: Mer information finns i [etablera data flöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
  - **Databaser**: Mer information finns i [etablera data flöde på en Azure Cosmos-databas](how-to-provision-database-throughput.md).
- I [Server](serverless.md) lös läge behöver du inte etablera några data flöden när du skapar resurser i ditt Azure Cosmos-konto. I slutet av fakturerings perioden debiteras du för den mängd enheter för programbegäran som har använts av databas åtgärderna.

## <a name="request-unit-considerations"></a>Överväganden för enhet för programbegäran

När du uppskattar antalet ru: er som konsumeras av arbets belastningen bör du tänka på följande faktorer:

* **Objektstorlek**: När storleken på ett objekt ökar, ökar även antalet enheter för programbegäran som används för att läsa eller skriva objektet.

* **Objektindexering**: Som standard indexeras alla objekt automatiskt. Färre enheter för programbegäran används om du väljer att inte indexera vissa av objekten i en container.

* **Antal objektegenskaper**: Om standardindexering används för alla egenskaper ökar det antal enheter för programbegäran som används för att skriva ett objekt allt eftersom antalet objektegenskaper ökar.

* **Indexerade egenskaper**: En indexeringsprincip för varje container avgör vilka egenskaper som indexeras som standard. Om du vill minska förbrukningen av enheter för programbegäran för skrivåtgärder bör du begränsa antalet indexerade egenskaper.

* **Data konsekvens**: de starka och gränsade inaktuella konsekvens nivåerna förbrukar ungefär två gånger mer ru: er medan Läs åtgärder utförs jämfört med andra avslappnade konsekvens nivåer.

* **Typ av läsningar**: Poäng läsningar kostar betydligt färre ru: er än frågor.

* **Frågemönster**: Komplexiteten i en fråga påverkar hur många enheter för programbegäran som förbrukas för en åtgärd. Faktorer som påverkar kostnaden för frågeåtgärder omfattar: 
    
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
* Lär dig mer om att utan [Server på Azure Cosmos DB](serverless.md).
* Läs mer om [logiska partitioner](partition-data.md).
* Lär dig mer om hur du [globalt skalar allokerat data flöde](scaling-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
* Lär dig hur du [hittar enhets avgiften för en åtgärd](find-request-unit-charge.md).
* Lär dig hur du [optimerar den etablerade data flödes kostnaden i Azure Cosmos DB](optimize-cost-throughput.md).
* Lär dig hur du [optimerar läsning och skrivning av kostnader i Azure Cosmos DB](optimize-cost-reads-writes.md).
* Lär dig hur du [optimerar kostnad för frågor i Azure Cosmos DB](optimize-cost-queries.md).
* Lär dig hur du [använder mått för att övervaka data flödet](use-metrics.md).
