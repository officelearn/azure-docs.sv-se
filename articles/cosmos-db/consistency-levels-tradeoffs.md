---
title: Azure Cosmos DB konsekvens, tillgänglighet och prestanda kompromisser
description: Tillgänglighets- och prestandaavvägningar för olika konsekvensnivåer i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 7cdaa9699b15000359c438bcc410e300415b759a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379951"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisser avseende konsekvens, tillgänglighet och prestanda

Avvägningar måste göras för distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, låg svarstid eller båda. Avvägningarna är mellan läskonsekvens kontra tillgänglighet, svarstid och dataflöde.

Azure Cosmos DB närmar sig datakonsekvens som ett spektrum av val. Detta tillvägagångssätt innehåller fler alternativ än de två ytterligheterna av stark och eventuell konsekvens. Du kan välja mellan fem väldefinierade nivåer på konsekvensspektrumet. Från starkaste till svagaste, nivåerna är:

- *Stark*
- *Begränsad föråldring*
- *Session*
- *Konsekvent prefix*
- *Eventuell*

Varje nivå ger tillgänglighet och prestanda kompromisser och backas upp av omfattande SLA.

## <a name="consistency-levels-and-latency"></a>Konsekvensnivåer och svarstid

Lässvarstiden för alla konsekvensnivåer är alltid garanterad att vara mindre än 10 millisekunder vid den 99:e percentilen. Den här lässvarstiden stöds av serviceavtalet. Den genomsnittliga lässvarstiden, vid den 50:e percentilen, är vanligtvis 4 millisekunder eller mindre.

Skrivfördröjningen för alla konsekvensnivåer är alltid garanterad att vara mindre än 10 millisekunder vid den 99:e percentilen. Den här skrivfördröjningen stöds av serviceavtalet. Den genomsnittliga skrivfördröjningen, vid den 50:e percentilen, är vanligtvis 5 millisekunder eller mindre. Azure Cosmos-konton som sträcker sig över flera regioner och har konfigurerats med stark konsekvens är ett undantag från den här garantin.

### <a name="write-latency-and-strong-consistency"></a>Skrivfördröjning och stark konsekvens

För Azure Cosmos-konton som konfigurerats med stark konsekvens med mer än en region är skrivsvarstiden lika med två gånger rundresa (RTT) mellan någon av de två mest avlägsna regionerna, plus 10 millisekunder vid den 99:e percentilen. RtT för högt nätverk mellan regionerna kommer att översätta till högre latens för Cosmos DB-begäranden eftersom stark konsekvens slutför en åtgärd först efter att ha säkerställt att den har åtagit sig att alla regioner inom ett konto.

Den exakta RTT-svarstiden är en funktion av avstånd mellan hastigheter och Azure-nätverkstopologin. Azure-nätverk ger inga svarstider SLA för RTT mellan två Azure-regioner. För ditt Azure Cosmos-konto visas replikeringsdydser i Azure-portalen. Du kan använda Azure-portalen (gå till bladet Mått, välj fliken Konsekvens) för att övervaka replikeringsdystren mellan olika regioner som är associerade med ditt Azure Cosmos-konto.

> [!IMPORTANT]
> Stark konsekvens för konton med regioner som spänner över mer än 8000 kilometer blockeras som standard på grund av hög skrivfördröjning. Kontakta supporten för att aktivera den här funktionen.

## <a name="consistency-levels-and-throughput"></a>Konsekvensnivåer och dataflöde

- För stark och begränsad föråldring görs läsningar mot två repliker i en fyra replikuppsättning (minoritetskvorum) för att ge konsekvensgarantier. Session, konsekvent prefix och eventuellt göra enstaka replikläsningar. Resultatet är att läsflöde för stark och begränsad föråldring för samma antal förfråga enheter är hälften av de andra konsekvensnivåerna för samma antal förfråga enheter.

- För en viss typ av skrivåtgärd, till exempel infoga, ersätta, upsert och ta bort, är skrivflödet för begäranheter identiska för alla konsekvensnivåer.

|**Konsekvensnivå**|**Kvorum läser**|**Kvorum skriver**|
|--|--|--|
|**Stark**|Lokal minoritet|Global majoritet|
|**Begränsad föråldring**|Lokal minoritet|Lokal majoritet|
|**Session**|Enkel replik (med sessionstoken)|Lokal majoritet|
|**Konsekvent prefix**|En enda replik|Lokal majoritet|
|**Eventuell**|En enda replik|Lokal majoritet|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Konsekvensnivåer och datahållbarhet

I en globalt distribuerad databasmiljö finns ett direkt samband mellan konsekvensnivå och datahållbarhet i närvaro av ett avbrott i hela regionen. När du utvecklar din kontinuitetsplan måste du förstå den maximala godtagbara tiden innan programmet återhämtar sig helt efter en störande händelse. Den tid som krävs för att ett program ska kunna återställas helt kallas **återställningstidsmål** **(RTO).** Du måste också förstå den maximala perioden för de senaste datauppdateringarna som programmet kan tolerera att förlora när du återställer efter en störande händelse. Tidsperioden för uppdateringar som du kanske har råd att förlora kallas **återställningspunkt mål** **(RPO**).

Tabellen nedan definierar förhållandet mellan konsekvensmodell och datahållbarhet i närvaro av ett områdesomfattande avbrott. Det är viktigt att notera att i ett distribuerat system, även med stark konsekvens, är det omöjligt att ha en distribuerad databas med en RPO och RTO på noll på grund av cap Theorem. Mer information om varför finns [i Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).

|**Region(er)**|**Replikeringsläge**|**Konsekvensnivå**|**RPO**|**Rto**|
|---------|---------|---------|---------|---------|
|1|Enkel eller multi-master|Eventuell konsekvensnivå|< 240 minuter|<1 vecka|
|>1|Enkelhanterare|Session, konsekvent prefix, eventuellt|< 15 minuter|< 15 minuter|
|>1|Enkelhanterare|Begränsad föråldring|*K* & *T*|< 15 minuter|
|>1|Enkelhanterare|Stark|0|< 15 minuter|
|>1|Multi-Master|Session, konsekvent prefix, eventuellt|< 15 minuter|0|
|>1|Multi-Master|Begränsad föråldring|*K* & *T*|0|

*K* = Antalet *"K"-versioner* (dvs. uppdateringar) av ett objekt.

*T* = Tidsintervallet *"T"* sedan den senaste uppdateringen.

## <a name="strong-consistency-and-multi-master"></a>Stark konsekvens och multi-master

Cosmos-konton som konfigurerats för multi-master kan inte konfigureras för stark konsekvens eftersom det inte är möjligt för ett distribuerat system att tillhandahålla en RPO på noll och en RTO på noll. Dessutom finns det inga skrivfördröjningsfördelar för att använda stark konsekvens med multi-master eftersom alla skrivningar till en region måste replikeras och bekräftas till alla konfigurerade regioner i kontot. Detta resulterar i samma skrivfördröjning som ett enda huvudkonto.

## <a name="next-steps"></a>Nästa steg

Läs mer om global distribution och allmänna konsekvensavvägningar i distribuerade system. Se följande artiklar:

- [Konsekvens kompromisser i modern distribuerad databas system design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Hög tillgänglighet](high-availability.md)
- [DLA för Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
