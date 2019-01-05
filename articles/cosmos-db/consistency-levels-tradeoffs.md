---
title: Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB
description: Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 52f08511d16e216ced7e3d1de11eae960cdbaeb8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041874"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, låg latens eller båda måste göra kompromisser. Kompromisser är mellan läsningskontinuitet jämfört med tillgänglighet, svarstid och dataflöde. 

Azure Cosmos DB närmar sig datakonsekvens som ett spektrum av alternativ. Den här metoden innehåller fler alternativ än två extremval med stark och slutlig konsekvens. Du kan välja mellan fem väldefinierade modeller på konsekvensspektrumet. Från starkast till svagast, modellerna är:

- Stark 
- Begränsad föråldring 
- Session 
- Konsekvent prefix 
- Eventuell 

Varje modell ger tillgänglighet och prestanda kompromisser och backas upp av ett omfattande SLA.

## <a name="consistency-levels-and-latency"></a>Konsekvensnivåer och svarstider

- Lässvarstid för alla konsekvensnivåer garanteras alltid ska vara mindre än 10 millisekunder i 99: e percentilen. Den här lässvarstid backas upp av serviceavtalet. Den genomsnittliga läsfördröjning den 50: e percentilen är vanligtvis 2 millisekunder eller mindre. Azure Cosmos-konton som sträcker sig över flera regioner och är konfigurerade med stark konsekvens är ett undantag garantin.

-  Skrivfördröjningen för de återstående konsekvensnivåerna garanteras alltid ska vara mindre än 10 millisekunder i 99: e percentilen. Den här skrivfördröjningen backas upp av serviceavtalet. Genomsnittligt antal skrivåtgärder svarstid, den 50: e percentilen är vanligtvis 5 millisekunder eller mindre.

Vissa Azure-Cosmos-konton kan ha flera regioner som har konfigurerats med stark konsekvens. I det här fallet garanteras skrivfördröjningen vara mindre än två gånger fram och åter tid plus 10 millisekunder i 99: e percentilen. RTT mellan de olika områdena längst bort är associerad med ditt Azure Cosmos-konto. Det är lika med RTT mellan de två längst bort regioner som är associerade med ditt Azure Cosmos-konto. Det här alternativet är för närvarande i förhandsversion. 

Den exakta RTT svarstiden är en funktion av hastighet ljus avståndet och Azure nätverkstopologin. Azure-nätverk ger inte någon fördröjning serviceavtal för RTT mellan två regioner som Azure. För ditt Azure Cosmos-konto visas replikeringsfördröjningar i Azure-portalen. Du kan använda Azure-portalen för att övervaka replikeringsfördröjningar mellan olika regioner som associeras med ditt konto.

## <a name="consistency-levels-and-throughput"></a>Konsekvensnivåer och dataflöde

- Ange ungefär två gånger läsgenomströmning jämfört med starka och begränsad föråldring för samma antal enheter för programbegäran, session, konsekventa prefix och slutlig konsekvensnivåer.

- För en viss typ av skrivåtgärd som infoga, Ersätt, upsert och ta bort, är genomströmning för skrivning för begäransenheter identiska för alla konsekvensnivåer.

## <a name="next-steps"></a>Nästa steg

Läs mer om global distribution och allmän konsekvens kompromisser i distribuerade system. Se följande artiklar:

* [Konsekvens kompromisser i moderna distribuerad databas systemdesign](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Hög tillgänglighet](high-availability.md)
* [Azure Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
