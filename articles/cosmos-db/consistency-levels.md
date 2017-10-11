---
title: "Konsekvensnivåer i Azure Cosmos DB | Microsoft Docs"
description: "Azure Cosmos-DB har fem konsekvensnivåer för att belastningsutjämna eventuell konsekvens, tillgänglighet och svarstid kompromisser."
keywords: slutliga konsekvensen azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: mimig1
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1ebec2285982c70aa9dc49950769fe18e2e2d0d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Data justerbara konsekvensnivåer i Azure Cosmos DB
Azure Cosmos-DB är utformad från grunden upp med global distributionsplatsen i åtanke för varje datamodell. Den är utformad att erbjuda förutsägbar låg latens garantier, en 99,99% tillgänglighet SLA och flera väldefinierade Avslappnad konsekvenskontroll modeller. För närvarande Azure Cosmos DB innehåller fem konsekvensnivåer: stark, begränsat föråldrad, session, konsekvent prefix och eventuell. 

Förutom **starkt** och **slutliga konsekvensen** modeller ofta erbjuds av distribuerade databaser Azure Cosmos DB erbjuder tre mer noggrant kodade och operationalized konsekvenskontroll modeller och har verifierat sina användbarhet mot verkligheten användningsfall. Det här är den **avgränsas föråldrad**, **session**, och **konsekvent prefixet** konsekvensnivåer. Gemensamt att dessa fem konsekvensnivåer du kan göra välmotiverat avvägningarna mellan konsekvens, tillgänglighet och svarstid. 

## <a name="distributed-databases-and-consistency"></a>Distribuerade databaser och konsekvenskontroll
Kommersiellt distribuerade databaser är indelade i två kategorier: databaser som inte alls erbjuder väldefinierade, beprövade konsekvensval och databaser som erbjuder två extremt programmerbara val (stark kontra eventuell konsekvens). 

Den tidigare tynger programmerare med detaljerna i sina replikeringsprotokoll och förväntar sig att de ska göra svåra avvägningar mellan konsekvens, tillgänglighet, svarstid och dataflöde. Den senare tvingar en att välja ett av två extremval. Trots mängden forskning och förslag på över 50 konsekvensmodeller, har den distribuerade databascommunityn inte kunna kommersialisera konsekvensnivåer utöver stark och eventuell konsekvens. Cosmos DB gör att utvecklare kan välja mellan fem väldefinierade konsekvenskontroll längs konsekvenskontroll spektrumet – starkt, avgränsas föråldrad, [session](http://dl.acm.org/citation.cfm?id=383631), konsekvent prefix och eventuell. 

![Azure Cosmos DB erbjuder flera väldefinierade (avslappnade) konsekvensmodeller att välja mellan](./media/consistency-levels/five-consistency-levels.png)

Följande tabell visar de specifika garantier som varje konsekvensnivå erbjuder.
 
**Konsekvensnivåer och garantier**

| Konsekvensnivå | Garantier |
| --- | --- |
| Stark | Lineariserbarhet |
| Begränsad föråldring | Konsekvent Prefix. Läsningar släpar efter skrivningar med k-prefix eller t-intervall |
| Session   | Konsekvent Prefix. Monotoniska läsningar, monotoniska skrivningar, läs-dina-skrivningar, skrivning-följer-läsning |
| Konsekvent prefix | De uppdateringar som returneras är något prefix av alla uppdateringar, utan några mellanrum |
| Eventuell  | Oordnade läsningar |

Du kan konfigurera standard-konsekvensnivå på ditt Cosmos DB-konto (och senare åsidosätta konsekvens för en specifik läsbegäran). Internt, gäller konsekvens standardnivå för data i partitionsuppsättningar som kan sträcka sig över regioner. Om 73% av våra klientorganisationer använder sessionskonsekvens 20% föredrar avgränsas föråldrad. Vi se att cirka 3 procent av våra kunder experimentera med olika konsekvensnivåer ursprungligen innan reglera på en specifik konsekvenskontroll val för sina program. Vi kan också se att endast 2% av våra klientorganisationer åsidosätter konsekvensnivåer på grundval av per begäran. 

Läser hanteras i sessionen, konsekvent prefix i Cosmos-DB och slutliga konsekvensen är två gånger så låg som läser med starka eller begränsad föråldringskonsekvens. Cosmos DB har branschledande serviceavtal för omfattande 99,99% inklusive konsekvens garanterar tillsammans med tillgänglighet, genomflöde och svarstid. Vi använder en [linearizability layout](http://dl.acm.org/citation.cfm?id=1806634), som körs kontinuerligt i vår tjänst telemetri och öppet rapporterar eventuella överträdelser för konsekvens för dig. För begränsad föråldrad, vi övervaka och rapportera eventuella överträdelser till k och t gränser. För alla fem Avslappnad konsekvensnivåer, vi även rapportera den [probabilistic avgränsas föråldrad mått](http://dl.acm.org/citation.cfm?id=2212359) direkt till dig.  

## <a name="scope-of-consistency"></a>Omfånget för konsekvenskontroll
Granulariteten för konsekvenskontroll är begränsad till en enskild begäran. Write-förfrågan kan motsvarar en infoga, Ersätt, upsert eller ta bort transaktionen. Precis som med skrivningar, begränsas också en Läs/fråga-transaktion till en enskild begäran. Användaren kan behöva sidbryta via en stor resultatmängd, sträcker sig över flera partitioner, men varje läsa transaktion är begränsade till en enda sida och hanteras från inom en partition.

## <a name="consistency-levels"></a>Konsekvensnivåer
Du kan konfigurera en konsekvenskontroll Standardnivå på ditt konto som gäller för alla samlingar (och databaser) under Cosmos-DB-kontot. Använd konsekvenskontroll Standardnivå anges på kontot som standard alla läser och frågor som utfärdats för de användardefinierade resurserna. Kan du slappna av konsekvensnivå för en specifik Läs/fråga begäran med i varje API som stöds. Det finns fem typer av konsekvensnivåer som stöds av Azure Cosmos DB replikering protokoll som tillhandahåller en tydlig kompromiss mellan specifika konsekvens garanterar och prestanda, enligt beskrivningen i det här avsnittet.

**Starka**: 

* Stark konsekvens erbjuder en [linearizability](https://aphyr.com/posts/313-strong-consistency-models) garantera med läsningar garanteras att returnera den senaste versionen av ett objekt. 
* Stark konsekvens garanterar att en skrivning enbart är synliga när den har bevarats varaktigt av kvorum majoritet av replikerna. Skrivning har antingen synkront bevarats varaktigt av både primär och kvorum av sekundärservrar eller den avbröts. Läs bekräftas mottagandet alltid av en majoritet läsa kvorum, en klient inte kan se en ogenomförda eller partiell skrivning och alltid garanteras att läsa den senaste godkända skrivningen. 
* Azure DB Cosmos-konton som är konfigurerade för att använda stark konsekvens kan inte associera mer än en Azure-region med ett konto i Azure Cosmos DB. 
* Kostnaden för en läsning (i [programbegäran](request-units.md) förbrukas) med stark konsekvens är högre än session och slutlig, men samma som avgränsas föråldrad.

**Begränsat föråldrad**: 

* Begränsat föråldrad konsekvens garanterar att läsningar kan ligga efter skrivning av högst *K* versioner eller prefix för ett objekt eller *t* tidsintervallet. 
* Därför när du väljer begränsat föråldrad, ”föråldrad” kan konfigureras på två sätt: antal versioner *K* för objektet som läsningar lag bakom skrivningar och tidsintervallet *t* 
* Begränsat föråldrad erbjudanden totala globala ordning förutom i ”föråldrad fönstret”. Monotonisk skrivskyddade garantier finns inom en region både inom och utanför ”föråldrad fönstret”. 
* Begränsad föråldrad ger konsekvens bättre säkerhet än session eller slutliga konsekvensen. För globalt distribuerade program rekommenderar vi att du använder avgränsas föråldrad för scenarier där du vill ha stark konsekvens, utan också 99,99% tillgänglighet och låg latens. 
* Azure DB Cosmos-konton som har konfigurerats med begränsad föråldringskonsekvens kan associera valfritt antal Azure-regioner med sina Azure DB som Cosmos-konto. 
* Kostnaden för en läsning (vad gäller RUs förbrukas) är högre än sessionen och slutliga konsekvensen, men samma som stark konsekvens med begränsad föråldrad.

**Sessionen**: 

* Till skillnad från de globala konsekvensfel modeller som erbjuds av stark och avgränsas föråldrad konsekvensnivåer begränsas sessionskonsekvens till en klientsession. 
* Sessionskonsekvens är idealiskt för alla scenarier där en enhet eller användare sessionen ingår eftersom det garanterar monotonisk läsningar, monotonisk skrivningar och läsa garanterar att din egen skrivningar (RYW). 
* Sessionskonsekvens tillhandahåller förutsägbar konsekvenskontroll för en session och maximalt läsa genomströmning samtidigt som den erbjuder de lägsta fördröjningsskrivningarna och läsningar. 
* Azure DB Cosmos-konton som har konfigurerats med sessionskonsekvens kan associera valfritt antal Azure-regioner med sina Azure DB som Cosmos-konto. 
* Kostnaden för en läsning (vad gäller RUs förbrukas) med session konsekvensnivå är mindre än starkt som avgränsas föråldrad, men mer än slutliga konsekvensen

<a id="consistent-prefix"></a>
**Konsekvent prefixet**: 

* Konsekvent prefix garanterar att i frånvaron av ytterligare skrivningar replikerna i gruppen så småningom att Konvergera. 
* Konsekvent prefix garanterar att läsningar aldrig finns i oordning skrivningar. Om skrivningar utfördes i ordning `A, B, C`, och sedan en klient ser antingen `A`, `A,B`, eller `A,B,C`, men aldrig i oordning som `A,C` eller `B,A,C`.
* Azure DB Cosmos-konton som har konfigurerats med konsekvent prefix konsekvenskontroll kan associera valfritt antal Azure-regioner med sina Azure Cosmos DB-konto. 

**Eventuell**: 

* Slutliga konsekvensen garanterar att i frånvaron av ytterligare skrivningar replikerna i gruppen så småningom att Konvergera. 
* Slutliga konsekvensen är svagaste form av konsekvenskontroll om en klient kan hämta de värden som är äldre än de som har läst.
* Slutliga konsekvensen ger den svagaste läskonsekvensen, men erbjuder den lägsta fördröjningen för både läsningar och skrivningar.
* Azure DB Cosmos-konton som har konfigurerats med slutliga konsekvensen kan associera valfritt antal Azure-regioner med sina Azure DB som Cosmos-konto. 
* Kostnaden för en läsning (vad gäller RUs förbrukas) med den slutliga konsekvensen nivån är den lägsta av alla Azure Cosmos DB konsekvensnivåer.

## <a name="configuring-the-default-consistency-level"></a>Konfigurera standardnivå för konsekvenskontroll
1. I den [Azure-portalen](https://portal.azure.com/), i Jumpbar klickar du på **Azure Cosmos DB**.
2. I den **Azure Cosmos DB** bladet väljer databasen konto för att ändra.
3. I bladet konto klickar du på **standard konsekvenskontroll**.
4. I den **standard konsekvenskontroll** bladet välj ny konsekvensnivå och klicka på **spara**.
   
    ![Skärmbild syntaxmarkering inställningsikonen och standard konsekvenskontroll post](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Konsekvensnivåer för frågor
Som standard för användardefinierade resurser, konsekvens nivån för frågor är samma som konsekvensnivå för läsning. Indexet uppdateras synkront som standard på varje infoga, Ersätt eller borttagningen av ett objekt i Cosmos-DB-behållaren. Detta gör att frågorna ta hänsyn till samma konsekvensnivå som punkt läsningar. Du kan konfigurera vissa samlingar för att uppdatera deras index lazy medan Azure Cosmos DB skrivåtgärder optimerad och stöd för varaktigt volymer skrivningar, synkron index underhåll och betjänar konsekvent frågor. Lazy indexering ytterligare ökar prestandan för skrivning och är idealisk för bulk införandet scenarier när en arbetsbelastning är främst Läs aktiverat.  

| Indexering läge | Läser | Frågor |
| --- | --- | --- |
| Konsekvent (standard) |Välj från stark, begränsat föråldrad, session, konsekvent prefix eller senare |Välj från stark, begränsat föråldrad, session eller senare |
| Lazy |Välj från stark, begränsat föråldrad, session, konsekvent prefix eller senare |Eventuell |
| Ingen |Välj från stark, begränsat föråldrad, session, konsekvent prefix eller senare |Inte tillämpligt |

Med skrivskyddade förfrågningar, kan som du sänka nivån konsekvenskontroll för en specifik fråga begäran i varje API.

## <a name="next-steps"></a>Nästa steg
Om du vill göra mer läsning om konsekvensnivåer och kompromisser rekommenderar vi följande resurser:

* Doug Terry. Replikerade datakonsekvens beskrivs via baseball (video).   
  [https://www.YouTube.com/Watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
* Doug Terry. Replikerade datakonsekvens beskrivs via baseball.   
  [http://Research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.PDF](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* Doug Terry. Sessionen garantier för svagt konsekvent replikerade Data.   
  [http://DL.acm.org/citation.cfm?ID=383631](http://dl.acm.org/citation.cfm?id=383631)
* Mikael Abadi. Konsekvenskontroll kompromisser i moderna distribuerade system databasstruktur: Fästpunkten är endast en del av artikeln ”.   
  [http://Computer.org/CSDL/mags/CO/2012/02/mco2012020037-ABS.HTML](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph m Hellerstein, med Stoica. Probabilistic innanför föråldrad (PBS) för praktiska partiella beslutsförhet.   
  [http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.PDF](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* Werner Vogels. Eventuell konsekvent - Revisited.    
  [http://allthingsdistributed.com/2008/12/eventually_consistent.HTML](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* Moni Naor, Avishai ull, Load, kapacitet och tillgängligheten för kvorum system, SIAM Journal för databehandling, v.27 n.2, p.423-447, April 1998.
  [http://epubs.siam.org/DOI/ABS/10.1137/S0097539795281232](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* Sebastian Burckhardt, Chris Dern, Macanal Musuvathi, Roy Tan, rad: en fullständig och automatisk linearizability bricka, förfaranden för 2010 ACM SIGPLAN konferens om programmering språk design och implementeringslösning juni 05 10 2010, Stockholmsderbyt, Ontario, Kanada [doi > 10.1145/1806596.1806634] [http://dl.acm.org/citation.cfm?id=1806634](http://dl.acm.org/citation.cfm?id=1806634)
* Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, med Stoica Probabilistically avgränsas föråldrad för praktiska partiella beslutsförhet förfaranden VLDB särskilt tillskjutet v.5 n.8, p.776-787, April 2012 [http://dl.acm.org/citation.cfm?id=2212359](http://dl.acm.org/citation.cfm?id=2212359)
