---
title: Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer | Microsoft Docs
description: Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB.
keywords: konsekvens, prestanda, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 061a7c223d0e1c6fa7384d7defe9f84f470236ce
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244075"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels"></a>Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, svarstider, eller båda, se grundläggande förhållandet mellan läsningskontinuitet jämfört med availability1, latency2 och dataflöde. Azure Cosmos DB närmar sig datakonsekvens som ett spektrum av alternativ i stället för de två extremlägena stark och eventuell konsekvens. Cosmos DB gör det möjligt för utvecklare att välja mellan fem väldefinierade konsekvensmodeller från konsekvensspektrumet (starkast till svagast) – **stark**, **begränsad föråldring**, **session** , **konsekvent prefix**, och **eventuell**. Var och en av de fem konsekvensmodeller ger tydliga tillgänglighet och prestanda kompromisser och backas upp av omfattande serviceavtal.

## <a name="consistency-levels-and-latency"></a>Konsekvensnivåer och svarstider

- Den **läsfördröjning** för alla konsekvensnivåer garanteras att alltid vara mindre än 10 millisekunder i 99: e percentilen och backas upp av serviceavtalet. Genomsnittliga (vid den 50: e percentilen) Läs svarstiden är normalt 2 millisekunder eller mindre.
- Förutom Cosmos-konton som sträcker sig över flera regioner och konfigurerad med stark konsekvens i **skrivsvarstid** för alla konsekvensnivåer garanteras att alltid vara mindre än 10 millisekunder i 99: e percentilen och backas upp av den SERVICEAVTAL (SLA). Genomsnittligt antal (vid den 50: e percentilen) skrivåtgärder svarstiden är vanligtvis 5 millisekunder eller mindre.
- För Cosmos-konton med flera regioner som har konfigurerats med stark konsekvens (för närvarande i förhandsversion), den **skrivsvarstid** garanteras vara mindre än < (2 * RTT) + 10 millisekunder i 99: e percentilen. RTT mellan längst bort för någon av de två regioner som är associerade med ditt Cosmos-konto. Den exakta RTT svarstiden är en funktion av hastighet för ljus avståndet och exakta Azure nätverkets topologi. Azure-nätverk ger inte någon fördröjning serviceavtal för RTT mellan två regioner som Azure. Cosmos DB-replikeringsfördröjningar visas i Azure-portalen för ditt Cosmos-konto, så att du kan övervaka replikeringsfördröjningar mellan olika regioner som associeras med ditt Cosmos-konto.

## <a name="consistency-levels-and-throughput"></a>Konsekvensnivåer och dataflöde

- För samma mängd RU ger Session, konsekventa prefix och slutlig konsekvens cirka 2 X läsningsdataflöde jämfört med starka och begränsad föråldring.
- Genomströmning för skrivning för RU: er är identiska för alla konsekvensnivåer för en viss typ av Skrivåtgärden (till exempel Insert, Ersätt, Upsert, ta bort osv.).

## <a name="consistency-levels-and-durability"></a>Konsekvensnivåer och hållbarhet

Innan skrivning bekräftas till klienten, data är hållbarheten har säkerställts på genom att ett kvorum av repliker i den region som tar emot skrivningar. Dessutom om behållaren är konfigurerad med konsekvent indexeringsprincip, är indexet också synkront uppdateras, replikeras och hållbarheten har säkerställts av kvorum av repliker innan skrivningen bekräftas till klienten. 

I följande tabell sammanfattas fönstret potentiell dataförlust i händelse av ett regionalt haveri för Cosmos-konton som sträcker sig över flera regioner.

| **Konsekvensnivå** | **Fönstret för potentiella dataförluster i händelse av ett regionalt haveri** |
| - | - |
| Stark | Noll |
| Begränsad föråldring | Begränsat till ”föråldring fönstret” konfigurerats av utvecklaren för Cosmos-kontot |
| Session | Upp till 5 sekunder |
| Konsekvent prefix | Upp till 5 sekunder |
| Eventuell | Upp till 5 sekunder |

## <a name="next-steps"></a>Nästa steg

Sedan kan du läsa mer om global distribution och allmän konsekvens kompromisser i distribuerade system med hjälp av följande artiklar:

* [Konsekvens kompromisser i moderna distribuerad databas systemdesign](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Hög tillgänglighet](high-availability.md)
* [Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
