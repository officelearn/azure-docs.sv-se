---
title: Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB
description: Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ee0dc1bec39bf95cbf4f3bf7ecea92b877a78b88
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113761"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisser avseende konsekvens, tillgänglighet och prestanda 

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

- Skrivfördröjningen för de återstående konsekvensnivåerna garanteras alltid ska vara mindre än 10 millisekunder i 99: e percentilen. Den här skrivfördröjningen backas upp av serviceavtalet. Genomsnittligt antal skrivåtgärder svarstid, den 50: e percentilen är vanligtvis 5 millisekunder eller mindre.

Vissa Azure-Cosmos-konton kan ha flera regioner som har konfigurerats med stark konsekvens. I det här fallet garanteras skrivfördröjningen vara mindre än två gånger fram och åter tid plus 10 millisekunder i 99: e percentilen. RTT mellan de olika områdena längst bort är associerad med ditt Azure Cosmos-konto. Det är lika med RTT mellan de två längst bort regioner som är associerade med ditt Azure Cosmos-konto. Det här alternativet är för närvarande i förhandsversion.

Den exakta RTT svarstiden är en funktion av hastighet ljus avståndet och Azure nätverkstopologin. Azure-nätverk ger inte någon fördröjning serviceavtal för RTT mellan två regioner som Azure. För ditt Azure Cosmos-konto visas replikeringsfördröjningar i Azure-portalen. Du kan använda Azure-portalen för att övervaka replikeringsfördröjningar mellan olika regioner som associeras med ditt konto.

## <a name="consistency-levels-and-throughput"></a>Konsekvensnivåer och dataflöde

- Ange ungefär två gånger läsgenomströmning jämfört med starka och begränsad föråldring för samma antal enheter för programbegäran, session, konsekventa prefix och slutlig konsekvensnivåer.

- För en viss typ av skrivåtgärd som infoga, Ersätt, upsert och ta bort, är genomströmning för skrivning för begäransenheter identiska för alla konsekvensnivåer.

## <a name="consistency-levels-and-data-durability"></a>Konsekvens nivåer och data hållbarhet

I en globalt distribuerad databas för miljön att det finns en direkt relation mellan konsekvens nivå och data hållbarhet när det finns ett avbrott på inom hela regionen. Tabellen definierar relationen mellan förhållandet mellan konsekvens modell och data hållbarhet när det finns många avbrott i regionen. Det är viktigt att Observera att i ett distribuerat system, även med stark konsekvens är det omöjligt att ha en distribuerad databas med och RPO och RTO noll på grund av CAP-satsen. Mer information om varför finns [konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).

|**Region(s)**|**Replikeringsläge**|**Konsekvensnivå**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|En eller flera huvudservrar|Alla konsekvensnivå|< 240 minuter|< 1 vecka|
|>1|Single Master|Session, konsekventa Prefix, eventuell|< 15 minuter|< 15 minuter|
|>1|Single Master|Begränsad föråldring|K &AMP; T *|< 15 minuter|
|>1|Multi-Master|Session, konsekventa Prefix, eventuell|< 15 minuter|0|
|>1|Multi-Master|Begränsad föråldring|K &AMP; T *|0|
|>1|En eller flera huvudservrar|Stark|0|< 15 minuter|

* K & T = antalet ”K” versioner (uppdateringar) av ett objekt. Eller ”T” tidsintervall.



## <a name="next-steps"></a>Nästa steg

Läs mer om global distribution och allmän konsekvens kompromisser i distribuerade system. Se följande artiklar:

- [Konsekvens kompromisser i moderna distribuerad databas systemdesign](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Hög tillgänglighet](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
