---
title: Enheter för programbegäran och dataflöde i Azure Cosmos DB
description: Läs om hur du anger och beräkna programbegäran krav i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 72ec8332a3363d5336fb84b3390d5e44ced0b2e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926041"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

Med Azure Cosmos DB betalar du för dataflödet du etablera och den lagring som du förbrukar per timme. Dataflöde måste etableras för att kontrollera att tillräckligt med systemresurser är tillgängliga för Azure Cosmos-databasen hela tiden. Du behöver tillräckligt med resurser för att uppfylla eller överträffa de [Azure Cosmos DB-serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och tabellen. Varje API: et har en egen uppsättning databasåtgärder. Dessa åtgärder mellan enkla punkt läser och skriver till komplexa frågor. Varje Databasåtgärden förbrukar systemresurser baserat på komplexiteten för åtgärden. 

Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks av *programbegäran* (eller ru: er, för kort). Du kan tänka på enheter för programbegäran per sekund som valuta för dataflöde. Begäransenheter per sekund är en rate-baserade valuta. Den avlägsnar systemresurser, till exempel CPU, IOPS och minne som krävs för att genomföra databasåtgärder som stöds av Azure Cosmos DB. 

Kostnaden för att läsa en 1 KB artikel är 1 Begäransenhet (eller 1 RU). Alla andra databasåtgärder på samma sätt tilldelade en kostnader med hjälp av RU: er. Oavsett vilken API som du använder kan interagera med din Azure Cosmos-behållare, mäts alltid kostnader som ru: er. Om Databasåtgärden är en skrivning, mäts Läs- eller fråga, kostnader alltid i ru: er.

Följande bild visar den övergripande uppfattning om ru: er:

![Databasoperationer använda programbegäran](./media/request-units/request-units.png)

Om du vill hantera och planera kapaciteten, garanterar Azure Cosmos DB att antalet mediereserverade enheter för en viss databas-åtgärd över den angivna datauppsättningen är deterministisk. Du kan undersöka rubriken för att spåra hur många enheter för programbegäran som förbrukas av alla åtgärder i databasen. När du förstår de [faktorer som påverkar RU avgifter](request-units.md#request-unit-considerations) och krav på programmets dataflöde, kan du köra programmet kostnadseffektivt sätt.

Du kan etablera antalet mediereserverade enheter för ditt program på basis av per sekund i steg med 100 enheter för programbegäran per sekund. Om du vill skala det etablerade dataflödet för ditt program, kan du öka eller minska antalet mediereserverade enheter när som helst. Du kan skala i ökar eller minskar av 100 ru: er. Du kan göra dina ändringar genom programmering eller via Azure-portalen. Du debiteras per timme.

Du kan etablera dataflöde på två olika Precision: 

* **Behållare**: Mer information finns i [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* **Databaser**: Mer information finns i [etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Överväganden för begäran-enhet

Medan du uppskatta antalet enheter för programbegäran per sekund för att etablera, Tänk på följande faktorer:

* **Objektet storlek**: När storleken på ett objekt ökar, ökar även antalet mediereserverade enheter som används för att läsa eller skriva objektet.

* **Indexering av objekt**: Som standard indexeras automatiskt varje objekt. Färre ru: er används om du väljer att inte indexera vissa av dina objekt i en behållare.

* **Objektet antal egenskaper**: Under förutsättning att standard indexering är för alla egenskaper, antalet mediereserverade enheter som används för att skriva ett objekt ökar när objektet egenskapen Antal ökar.

* **Indexerade egenskaper**: En princip för index på varje behållare avgör vilka egenskaper indexeras som standard. Begränsa antalet indexerade egenskaper för att minska RU-förbrukningen för skrivåtgärder.

* **Datakonsekvens**: Konsekvensnivåer starka och begränsad föråldring förbrukar ungefär två gånger mer ru: er när du utför läsåtgärder jämfört med att andra restriktiva konsekvensnivåer.

* **Fråga efter mönster**: Komplexiteten för en fråga påverkar hur många enheter för programbegäran som förbrukas för en åtgärd. Faktorer som påverkar kostnaden för frågeåtgärder är: 
    
    - Antalet frågeresultat
    - Antalet predikat
    - Typen av predikat
    - Hur många användardefinierade funktioner
    - Storleken på datakällan
    - Storleken på resultatuppsättningen
    - Projektioner

  Azure Cosmos DB garanterar att samma fråga på samma data alltid kostar samma antal ru: er på upprepade körningar.

* **Skriptet användning**: Precis som med frågor, lagrade procedurer och utlösare för programbegäran för baserat på komplexiteten för åtgärder som utförs. När du utvecklar ditt program kan du granska den [kostnad begärandehuvudet](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) att bättre förstå hur mycket RU-kapacitet som förbrukar varje åtgärd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du [etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md).
* Läs mer om [logiska partitioner](partition-data.md).
* Mer information om hur du [globalt skala dataflöde](scaling-throughput.md).
* Lär dig hur du [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
* Lär dig hur du [hitta kostnaden för begäran om enheten för en åtgärd](find-request-unit-charge.md).
* Lär dig hur du [kostnadsoptimering etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md).
* Lär dig hur du [optimera läsningar och skrivningar kostnaden i Azure Cosmos DB](optimize-cost-reads-writes.md).
* Lär dig hur du [kostnadsoptimering fråga i Azure Cosmos DB](optimize-cost-queries.md).
