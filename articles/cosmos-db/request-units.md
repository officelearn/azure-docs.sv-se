---
title: Enheter för programbegäran och dataflöde i Azure Cosmos DB
description: Lär dig mer om hur du anger och beräkna krav för begäran-enhet i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: dd9fb81b37627518af1375aa9876e6c509b917c9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033935"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

Med Azure Cosmos DB betalar du för dataflödet du etablera och den lagring som du förbrukar per timme. Dataflöde måste etableras för att kontrollera att tillräckligt med systemresurser är tillgängliga för Azure Cosmos-databasen hela tiden att uppfylla eller överträffa serviceavtalet för Azure Cosmos DB.

Azure Cosmos DB stöder en mängd olika API: er (SQL, MongoDB, Cassandra, Gremlin och tabell). Varje API: et har en egen uppsättning databasoperationer, allt från enkla punkt läser och skriver till komplexa frågor. Varje Databasåtgärden förbrukar systemresurser beroende på komplexiteten för åtgärden.  Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks som programbegäran (ru: er). Kostnaden för att läsa en 1 KB-objekt är 1 Begäransenhet (1 RU) och minsta ru: er som krävs för att använda 1 GB lagringsutrymme är 40. Alla andra databasåtgärder på samma sätt tilldelade en kostnad när det gäller ru: er. Oavsett API som du använder för att interagera med din Azure Cosmos-behållare och Databasåtgärden (skriva, läsa, fråga), mätt kostnader alltid efter ru: er.

Du kan tänka på RU/s som valuta för dataflöde. RU/s är en rate-baserade valuta, vilket avlägsnar systemresurser, till exempel CPU, IOPS och minne som krävs för att genomföra databasåtgärder som stöds av Azure Cosmos DB. Följande bild visar de enheter för programbegäran som används av olika databasoperationer:

![Databasoperationer förbrukar enheter för programbegäran](./media/request-units/request-units.png)

Om du vill hantera och planera kapaciteten, garanterar Azure Cosmos DB att antalet mediereserverade enheter för en viss databas-åtgärd över den angivna datauppsättningen är deterministisk. Du kan spåra hur många enheter för programbegäran som förbrukas av alla Databasåtgärden genom att undersöka svarshuvudet. När du förstår de faktorer som påverkar begäran units och krav på programmets dataflöde kan du köra programmet ett kostnadseffektivt sätt.

Medan du faktureras per timme, kan du etablera antalet mediereserverade enheter för ditt program på basis av per sekund i steg på 100 RU/s. Skalar det etablerade dataflödet för ditt program, kan du öka eller minska antalet mediereserverade enheter (i ökar eller minskar av 100 ru: er) när som helst genom programmering eller via Azure portal.

Du kan etablera dataflöde på två olika Precision: 

* **Behållare**. Mer information finns i [hur du etablera dataflöde för en Azure Cosmos-behållare.](how-to-provision-container-throughput.md)
* **Databaser**. Mer information finns i [hur du etablera dataflöde för en Azure Cosmos-databas.](how-to-provision-database-throughput.md)

## <a name="request-unit-considerations"></a>Överväganden för begäran-enhet

När du uppskatta antalet RU/s att etablera, är det viktigt att tänka på följande faktorer:

* **Objektet storlek** – när storleken på ett objekt ökar antalet mediereserverade enheter som används för att läsa eller skriva objektet också ökar.

* **Indexering av objekt** -som standard indexeras automatiskt varje objekt. Färre begäransenheter förbrukas om du väljer att inte indexera vissa av dina objekt i en behållare.

* **Objektet antal egenskaper** -förutsatt att standard indexering för alla egenskaper, antalet mediereserverade enheter som används för att skriva ett objekt ökar när objektet egenskapen Antal ökar.

* **Indexerade egenskaper** – en index-princip för varje behållare avgör vilka egenskaper indexeras som standard. Du kan minska begäran Enhetsförbrukningen för skrivåtgärder genom att begränsa antalet indexerade egenskaper.

* **Datakonsekvens** -konsekvensnivåer starka och begränsad föråldring förbrukar ungefär 2 X mer ru: er när du utför läsåtgärder jämfört med att andra restriktiva konsekvensnivåer.

* **Fråga efter mönster** -komplexiteten för en fråga påverkar hur många enheter för programbegäran som förbrukas för en åtgärd. Antal frågeresultat, antal predikat, typen av predikat, hur många användardefinierade funktioner, storleken på datakällan, storleken på resultatet och projektioner påverkar kostnaden för frågeåtgärder. Azure Cosmos DB garanterar att samma fråga på samma data alltid kommer att kosta lika många ru: er på upprepningar körningar.

* **Skriptet användning** – med, lagrade procedurer och utlösare konsumera ru: er baserat på komplexiteten för åtgärder som utförs. När du utvecklar ditt program kan du granska kostnad rubriken för att bättre förstå hur mycket kapacitet för begäran-enhet som förbrukar varje åtgärd.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [etablering dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md)
* Läs mer om [logiska partitioner](partition-data.md)
* Läs mer om [globalt skala dataflöde](scaling-throughput.md)
* Lär dig [hur du etablera dataflöde för en Azure Cosmos-behållare](how-to-provision-container-throughput.md)
* Lär dig [hur du etablera dataflöde för en Azure Cosmos-databas](how-to-provision-database-throughput.md)
