---
title: Azure Cosmos DB konsekvens, tillgänglighet och prestanda kompromisser
description: Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 4e2cb2b93010478338cd40236403da4ca0ca99fc
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825266"
---
# <a name="latency-availability-and-performance-tradeoffs-with-different-azure-cosmos-db-consistency-levels"></a>Latens, tillgänglighet och prestanda kompromisser med olika Azure Cosmos DB konsekvens nivåer

Avvägningar måste göras för distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, låg svarstid eller båda. Avvägningarna är mellan läskonsekvens kontra tillgänglighet, svarstid och dataflöde.

Azure Cosmos DB närmar sig data konsekvens som ett spektrum av alternativ. Den här metoden innehåller fler alternativ än de två extrema och slutliga konsekvensen. Du kan välja mellan fem väldefinierade nivåer i konsekvens spektrumet. Från starkast till svagt, är nivåerna:

- *Stark*
- *Begränsad föråldrad*
- *Session*
- *Konsekvent prefix*
- *Slutliga*

Varje nivå ger till gång till tillgänglighets-och prestanda kompromisser och backas upp av omfattande service avtal.

## <a name="consistency-levels-and-latency"></a>Konsekvens nivåer och svars tid

Läs fördröjningen för alla konsekvens nivåer garanterar alltid att vara mindre än 10 millisekunder vid 99 percentil. Den här Läs fördröjningen backas upp av service avtalet. Den genomsnittliga Läs fördröjningen, vid den 50: e percentilen, är normalt 4 millisekunder eller mindre.

Skriv fördröjningen för alla konsekvens nivåer garanterar alltid att vara mindre än 10 millisekunder vid 99 percentil. Den här Skriv fördröjningen backas upp av service avtalet. Den genomsnittliga Skriv fördröjningen, vid den 50: e percentilen, är vanligt vis 5 millisekunder eller mindre. Azure Cosmos-konton som sträcker sig över flera regioner och som har kon figurer ATS med stark konsekvens är ett undantag till denna garanti.

### <a name="write-latency-and-strong-consistency"></a>Skriv fördröjning och stark konsekvens

För Azure Cosmos-konton som kon figurer ATS med stark konsekvens med mer än en region, är Skriv fördröjningen lika med två gånger tur och retur-tid (söksvar) mellan någon av de två precisaste regionerna, plus 10 millisekunder i 99 percentilen. Hög blandning av nätverk mellan regionerna översätts till högre latens för Cosmos DB begär Anden eftersom stark konsekvens bara slutför en åtgärd efter att ha kontrollerat att den har allokerats till alla regioner i ett konto.

Den exakta svars tiden för försvars tid är en funktion av hastigheten-lätt avstånd och Azure-nätverk sto pol Ogin. Azure-nätverk ger ingen latens-service avtal för sökrutan mellan två Azure-regioner. För ditt Azure Cosmos-konto visas replikeringens fördröjningar i Azure Portal. Du kan använda Azure Portal (gå till bladet mått, Välj fliken konsekvens) för att övervaka fördröjningen mellan olika regioner som är associerade med ditt Azure Cosmos-konto.

> [!IMPORTANT]
> Stark konsekvens för konton med regioner som sträcker sig över 5000 miles (8000 kilo meter) blockeras som standard på grund av hög Skriv fördröjning. Om du vill aktivera den här funktionen kontaktar du supporten.

## <a name="consistency-levels-and-throughput"></a>Konsekvens nivåer och data flöde

- För stark och begränsad föråldrad är läsningar gjorda mot två repliker i en fyra replik uppsättning (minoritets kvorum) för att tillhandahålla konsekvens garantier. Session, konsekvent prefix och gör det enkelt att läsa en enskild replik. Resultatet är att Läs data flöde för samma antal enheter för programbegäran är hälften av de andra konsekvens nivåerna.

- För en specifik typ av Skriv åtgärd, till exempel infoga, Ersätt, upsert och Delete, är Skriv data flödet för enheter för programbegäran identiskt för alla konsekvens nivåer.

|**Konsekvens nivå**|**Kvorum läsningar**|**Skrivningar till kvorum**|
|--|--|--|
|**Stark**|Lokal minoritet|Global majoritet|
|**Begränsad föråldrad**|Lokal minoritet|Lokal majoritet|
|**Session**|Enskild replik (med sessionstoken)|Lokal majoritet|
|**Konsekvent prefix**|Enskild replik|Lokal majoritet|
|**Slutliga**|Enskild replik|Lokal majoritet|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Konsekvens nivåer och data hållbarhet

I en globalt distribuerad databas miljö finns det ett direkt förhållande mellan konsekvens nivån och data hållbarhet i närvaro av ett områdes omfattande avbrott. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter en störnings händelse. Tiden som krävs för att ett program ska återställas fullständigt kallas för **återställnings tids mål** (**RTO**). Du måste också förstå hur lång tid det tar för nya data uppdateringar som programmet kan tolerera vid återställning efter en störnings händelse. Tids perioden för uppdateringar som du kanske har råd att förlora kallas för **återställnings punkt mål (återställnings punkt mål** ).**RPO**

I tabellen nedan definieras relationen mellan konsekvens modell och data hållbarhet i närvaro av ett brett avbrott i regionen. Det är viktigt att notera att i ett distribuerat system, även med stark konsekvens, är det omöjligt att ha en distribuerad databas med återställnings-och RTO noll på grund av CAP-satsen. Mer information om varför finns [i konsekvens nivåer i Azure Cosmos DB](consistency-levels.md).

|**Region (er)**|**Replikeringsläget**|**Konsekvensnivå**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|En eller flera Skriv regioner|Vilken konsekvens nivå som helst|< 240 minuter|<1 vecka|
|>1|Enskild Skriv region|Session, konsekvent prefix, eventuell|< 15 minuter|< 15 minuter|
|>1|Enskild Skriv region|Begränsad föråldring|*K*  &  *T*|< 15 minuter|
|>1|Enskild Skriv region|Stark|0|< 15 minuter|
|>1|Flera Skriv regioner|Session, konsekvent prefix, eventuell|< 15 minuter|0|
|>1|Flera Skriv regioner|Begränsad föråldring|*K*  &  *T*|0|

*K* = antalet *"K"* versioner (d.v.s. uppdateringar) för ett objekt.

*T* = tidsintervallet *"T"* sedan den senaste uppdateringen.

## <a name="strong-consistency-and-multiple-write-regions"></a>Stark konsekvens och flera Skriv regioner

Cosmos-konton som kon figurer ATS med flera Skriv regioner kan inte konfigureras för stark konsekvens eftersom det inte är möjligt för ett distribuerat system att leverera noll och en RTO på noll. Det finns dessutom inga fördelar med Skriv fördröjning för att använda stark konsekvens med flera Skriv regioner, eftersom Skriv förfrågan till valfri region måste replikeras och bekräftas för alla konfigurerade regioner i kontot. Detta resulterar i samma Skriv fördröjning som ett enda Skriv regions konto.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om global distribution och allmän konsekvens kompromisser i distribuerade system. Se följande artiklar:

- [Konsekvens i moderna distribuerade databas system design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Hög tillgänglighet](high-availability.md)
- [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
