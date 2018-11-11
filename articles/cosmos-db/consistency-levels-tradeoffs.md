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
ms.openlocfilehash: 0e4105d6f56a8eb45a83e970c85319cf25041781
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514782"
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

## <a name="next-steps"></a>Nästa steg

Sedan kan du läsa mer om global distribution och allmän konsekvens kompromisser i distribuerade system med hjälp av följande artiklar:

* [Konsekvens kompromisser i moderna distribuerad databas systemdesign](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Hög tillgänglighet](high-availability.md)
* [Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
