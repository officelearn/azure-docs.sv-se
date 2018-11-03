---
title: Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB | Microsoft Docs
description: Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB.
keywords: konsekvens, prestanda, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 8f36026c7e5802994b8cf22d60c6ecea052e6382
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963055"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, svarstider, eller båda, se grundläggande förhållandet mellan läsningskontinuitet jämfört med tillgänglighet, svarstid och dataflöde. Azure Cosmos DB närmar sig datakonsekvens som ett spektrum av alternativ i stället för de två extremlägena stark och eventuell konsekvens. Cosmos DB gör det möjligt för utvecklare att välja mellan fem väldefinierade konsekvensmodeller från konsekvensspektrumet (starkast till svagast) – **stark**, **begränsad föråldring**, **session** , **konsekvent prefix**, och **eventuell**. Var och en av de fem konsekvensmodeller ger tillgänglighet och prestanda kompromisser och backas upp av omfattande serviceavtal.

## <a name="consistency-levels-and-latency"></a>Konsekvensnivåer och svarstider

- Den **läsfördröjning** för alla konsekvensnivåer garanteras att alltid vara mindre än 10 millisekunder i 99: e percentilen och backas upp av serviceavtalet. Genomsnittliga (vid den 50: e percentilen) Läs svarstiden är normalt 2 millisekunder eller mindre.

- Förutom de Cosmos-konton som sträcker sig över flera regioner och är konfigurerade med stark konsekvens i **skrivsvarstid** återstående konsekvent nivåer garanteras att alltid vara mindre än 10 millisekunder vid 99th : e percentilen. Den här skrivfördröjningen backas upp av serviceavtalet. Genomsnittligt antal (vid den 50: e percentilen) skrivåtgärder svarstiden är vanligtvis 5 millisekunder eller mindre.

- För Cosmos-konton som har flera regioner som har konfigurerats med stark konsekvens (för närvarande i förhandsversion), den **skrivsvarstid** garanteras vara mindre än < (2 * tidszonsbevarande tid/RTT) + 10 millisekunder i 99: e percentilen. RTT mellan de två längst bort regioner som är associerade med ditt Cosmos-konto. Den exakta RTT svarstiden är en funktion av hastighet för ljus avståndet och exakta Azure nätverkets topologi. Azure-nätverk ger inte någon fördröjning serviceavtal för RTT mellan två regioner som Azure. Cosmos DB-replikeringsfördröjningar visas i Azure-portalen för ditt Cosmos-konto, så att du kan övervaka replikeringsfördröjningar mellan olika regioner som associeras med ditt Cosmos-konto.

## <a name="consistency-levels-and-throughput"></a>Konsekvensnivåer och dataflöde

- För samma antal enheter för programbegäran ger session, konsekventa prefix och slutlig konsekvensnivåer cirka 2 X läsningsdataflöde jämfört med starka och begränsad föråldring.

- Genomströmning för skrivning för begäransenheter är identiska för alla konsekvensnivåer för en viss typ av Skrivåtgärden infoga, Ersätt, upsert, ta bort osv.

## <a name="consistency-levels-and-durability"></a>Konsekvensnivåer och hållbarhet

Innan skrivning bekräftas till klienten, är data hållbarheten har säkerställts genom att ett kvorum av repliker i den region som accepterar skrivåtgärder. Dessutom om behållaren är konfigurerad med konsekvent indexeringsprincip, är indexet också synkront uppdateras, replikeras och hållbarheten har säkerställts av kvorum av repliker innan bekräftelse av Skrivåtgärden skickas till klienten.

I följande tabell sammanfattas potentiella dataförlustfönstret i händelse av ett regionalt haveri för Cosmos-konton som sträcker sig över flera regioner.

| **Konsekvensnivå** | **Potentiella dataförlustfönstret i händelse av ett regionalt haveri** |
| - | - |
| Stark | Noll |
| Begränsad föråldring | Begränsat till ”föråldring fönstret” du konfigurerar på Cosmos-konto. |
| Session | Upp till 5 sekunder |
| Konsekvent prefix | Upp till 5 sekunder |
| Eventuell | Upp till 5 sekunder |

## <a name="next-steps"></a>Nästa steg

Sedan kan du läsa mer om global distribution och allmän konsekvens kompromisser i distribuerade system med hjälp av följande artiklar:

* [Konsekvens kompromisser i moderna distribuerad databas systemdesign](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Hög tillgänglighet](high-availability.md)
* [Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
