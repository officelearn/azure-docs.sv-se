---
title: Enheter för programbegäran och dataflöde i Azure Cosmos DB
description: Läs om hur du anger och beräkna programbegäran krav i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: 3801c19fbef70bf5d67670c4d9acc950291853e6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990163"
---
# <a name="request-units-in-azure-cosmos-db"></a>Enheter för programbegäran i Azure Cosmos DB

Med Azure Cosmos DB betalar du för dataflödet du etablera och den lagring som du förbrukar per timme. Dataflöde måste etableras för att kontrollera att tillräckligt med systemresurser är tillgängliga för Azure Cosmos-databasen hela tiden. Du behöver tillräckligt med resurser för att uppfylla eller överträffa serviceavtalet för Azure Cosmos DB.

Azure Cosmos DB stöder många API: er, till exempel SQL, MongoDB, Cassandra, Gremlin och tabellen. Varje API: et har en egen uppsättning databasåtgärder. Dessa åtgärder mellan enkla punkt läser och skriver till komplexa frågor. Varje Databasåtgärden förbrukar systemresurser baserat på komplexiteten för åtgärden. 

Kostnaden för alla databasåtgärder normaliseras av Azure Cosmos DB och uttrycks av programbegäran (ru: er). Kostnaden för att läsa en 1 KB-objekt är 1 Begäransenhet (RU). Alla andra databasåtgärder tilldelas på samma sätt en kostnad genom att använda ru: er. Oavsett vilken API som du använder kan interagera med din Azure Cosmos-behållare, mäts alltid kostnader som ru: er. Om Databasåtgärden är en skrivning, mäts Läs- eller fråga, kostnader alltid i ru: er.

Du kan tänka på enheter för programbegäran per sekund som valuta för dataflöde. Begäransenheter per sekund är en rate-baserade valuta. Den avlägsnar systemresurser, till exempel CPU, IOPS och minne som krävs för att genomföra databasåtgärder som stöds av Azure Cosmos DB. Följande bild visar ru: er som används av olika databasoperationer:

![Databasoperationer använda programbegäran](./media/request-units/request-units.png)

Om du vill hantera och planera kapaciteten, garanterar Azure Cosmos DB att antalet mediereserverade enheter för en viss databas-åtgärd över den angivna datauppsättningen är deterministisk. Granska rubriken för att spåra hur många enheter för programbegäran som förbrukas av alla åtgärder i databasen. När du förstår de faktorer som påverkar RU avgifter och krav på programmets dataflöde kan köra du dina programkostnaden effektivt.

Du faktureras per timme. Du kan etablera antalet mediereserverade enheter för ditt program på basis av per sekund i steg med 100 enheter för programbegäran per sekund. Om du vill skala det etablerade dataflödet för ditt program, kan du öka eller minska antalet mediereserverade enheter när som helst. Gör dina ändringar i ökar eller minskar av 100 ru: er. Du kan göra dina ändringar genom programmering eller via Azure-portalen.

Du kan etablera dataflöde på två olika Precision: 

* **Behållare**. Mer information finns i [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* **Databaser**. Mer information finns i [etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Överväganden för begäran-enhet

Medan du uppskatta antalet enheter för programbegäran per sekund för att etablera, Tänk på följande faktorer:

* **Objektet storlek**: När storleken på ett objekt ökar, ökar även antalet mediereserverade enheter som används för att läsa eller skriva objektet.

* **Indexering av objekt**: Som standard indexeras automatiskt varje objekt. Färre ru: er används om du väljer att inte indexera vissa av dina objekt i en behållare.

* **Objektet antal egenskaper**: Under förutsättning att standard indexering för alla egenskaper, antalet mediereserverade enheter som används för att skriva ett objekt ökar när objektet egenskapen Antal ökar.

* **Indexerade egenskaper**: En princip för index på varje behållare avgör vilka egenskaper indexeras som standard. Begränsa antalet indexerade egenskaper för att minska RU-förbrukningen för skrivåtgärder.

* **Datakonsekvens**: Konsekvensnivåer starka och begränsad föråldring förbrukar ungefär två gånger mer ru: er när du utför läsåtgärder jämfört med att andra restriktiva konsekvensnivåer.

* **Fråga efter mönster**: Komplexiteten för en fråga påverkar hur många enheter för programbegäran som förbrukas för en åtgärd. Faktorer som påverkar kostnaden för frågeåtgärder är: 
    
    - Antal frågeresultat.
    - Antal predikat.
    - Typen av predikat.
    - Hur många användardefinierade funktioner.
    - Storleken på datakällan.
    - Storleken på resultatuppsättningen.
    - Projektioner.

  Azure Cosmos DB garanterar att samma fråga på samma data alltid kostar samma antal ru: er på upprepade körningar.

* **Skriptet användning**: Precis som med frågor, lagrade procedurer och utlösare för programbegäran för baserat på komplexiteten för åtgärder som utförs. När du utvecklar ditt program kan du granska kostnad rubriken för att bättre förstå hur mycket RU-kapacitet som förbrukar varje åtgärd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du [etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md).
* Läs mer om [logiska partitioner](partition-data.md).
* Mer information om hur du [globalt skala dataflöde](scaling-throughput.md).
* Lär dig hur du [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
