---
title: Konsekvensnivåer i Azure Cosmos DB | Microsoft Docs
description: Azure Cosmos DB har fem konsekvensnivåer för att belastningsutjämna slutlig konsekvens, tillgänglighet och svarstid med de.
keywords: slutlig konsekvens, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d95790dc09f6d26c6ae749ed0cd386053c5cb35
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42055782"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Data justerbara konsekvensnivåer i Azure Cosmos DB
Azure Cosmos DB har utformats från grunden upp med global distribution i åtanke för varje datamodellen. Den är utformad att erbjuda garantier för förutsägbar med låg latens och flera väldefinierade Avslappnad konsekvensmodeller. För närvarande kan Azure Cosmos DB innehåller fem konsekvensnivåer: stark, bunden utgång, session, enhetligt prefix och slutlig. Bunden utgång, session, konsekventa prefix och slutlig är kallas ”Avslappnad konsekvensmodeller” eftersom de tillhandahåller mindre konsekvent än stark, vilket är i de flesta mycket konsekvent modell som är tillgänglig. 

Förutom den **stark** och **eventuell konsekvens** modeller ofta erbjuds av distribuerade databaser, Azure Cosmos DB erbjuder tre mer noggrant klassificering och produktionsslutpunkt konsekvensmodeller:  **begränsad föråldring**, **session**, och **konsekvent prefix**. Led i var och en av dessa konsekvensnivåer har verifierats mot verkliga användningsfall. Sammantaget att dessa fem konsekvensnivåer du kan göra väl motiverad avvägningarna mellan konsekvens, tillgänglighet och svarstid. 

I följande videoklipp visar Azure Cosmos DB-Programhanteraren Andrew Liu nyckelfärdig global distribution-funktioner.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Distribuerade databaser och konsekvens
Kommersiellt distribuerade databaser är indelade i två kategorier: databaser som inte erbjuder väldefinierade konsekvensval konsekvensval alls och databaser som erbjuder två programmerbara val (stark kontra eventuell konsekvens). 

Den tidigare tynger programmerare med detaljerna i sina replikeringsprotokoll och förväntar sig att de ska göra svåra avvägningar mellan konsekvens, tillgänglighet, svarstid och dataflöde. Den senare tvingar en att välja ett av två extremval. Trots mängden forskning och förslag på över 50 konsekvensmodeller, har den distribuerade databascommunityn inte kunna kommersialisera konsekvensnivåer utöver stark och eventuell konsekvens. Cosmos DB kan utvecklare välja mellan fem väldefinierade konsekvensmodeller längs konsekvensspektrumet: stark, begränsad föråldring, [session](http://dl.acm.org/citation.cfm?id=383631), konsekvent prefix och slutlig. 

![Azure Cosmos DB erbjuder flera väldefinierade (avslappnade) konsekvensmodeller att välja mellan](./media/consistency-levels/five-consistency-levels.png)

Följande tabell visar de specifika garantier som varje konsekvensnivå erbjuder.
 
**Konsekvensnivåer och garantier**

| Konsekvensnivå | Garantier |
| --- | --- |
| Stark | Linjärbarhetsgaranti. Läsningar är garanterade att returnera den senaste versionen av ett objekt.|
| Begränsad föråldring | Konsekvent Prefix. Läsningar släpar efter skrivningar med högst k-prefix eller t-intervall |
| Session   | Konsekvent Prefix. Monotoniska läsningar, monotoniska skrivningar, läs-dina-skrivningar, skrivning-följer-läsning |
| Konsekvent prefix | De uppdateringar som returneras är något prefix av alla uppdateringar, utan några mellanrum |
| Eventuell  | Oordnade läsningar |

Du kan konfigurera standard-konsekvensnivå på ditt Cosmos DB-konto (och senare åsidosätta konsekvens för en specifik läsbegäran). Internt gäller standardkonsekvensnivå data inom partitionsuppsättningarna vilket kan sträcka sig över regioner. Föredrar begränsad föråldring ca 73% av Azure Cosmos DB-klienter använda sessionskonsekvens och 20%. Cirka 3% av Azure Cosmos DB kunder experimentera med olika konsekvensnivåer inledningsvis innan reglera på en specifik konsekvens val för sina program. Endast 2% med Azure Cosmos DB-klienter åsidosätta konsekvensnivåer på varje begäran. 

Läsningar serveras session, konsekventa prefix och slutlig konsekvens är två gånger så låg som läsningar med stark eller begränsad föråldring, konsekvens i Cosmos DB. Cosmos DB har branschledande omfattande serviceavtal inklusive konsekvensgarantier tillsammans med tillgänglighet, dataflöde och svarstid. Azure Cosmos DB använder en [linjärbarhetsgaranti förutsättningar](http://dl.acm.org/citation.cfm?id=1806634), som körs kontinuerligt via tjänsttelemetri och öppet rapporterar överträdelser konsekvens för dig. För begränsad föråldring, Azure Cosmos DB övervakar och rapporterar eventuella överträdelser till k och t gränser. För alla fem Avslappnad konsekvensnivåer, Azure Cosmos DB även rapporterar den [probabilistically bunden föråldring mått](http://dl.acm.org/citation.cfm?id=2212359) direkt till dig.  

## <a name="service-level-agreements"></a>Servicenivåavtal

Azure Cosmos DB erbjuder omfattande 99,99% [serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) vilka garanti dataflöde, konsekvens, tillgänglighet och svarstid för Azure Cosmos DB-databas konton som är begränsade till en enda Azure-region som konfigurerats med någon av de fem konsekvensen nivåer eller databaskonton för flera Azure-regioner, konfigurerats med någon av fyra Avslappnad konsekvensnivåer. Dessutom är oberoende av val av en konsekvensnivå, Azure Cosmos DB erbjuder ett serviceavtal på 99,999% lästillgänglighet för databaskonton i två eller flera Azure-regioner.

## <a name="scope-of-consistency"></a>Omfattningen av konsekvens
Granulariteten för konsekvens är begränsad till en enskild begäran. En skrivbegäran kan motsvara en insert-, Ersätt, upsert eller ta bort transaktion. Precis som med skrivningar, begränsas även en Läs/fråga transaktion till en enskild begäran. Användaren kan komma att behöva sidbryta över en stor resultatuppsättningen, sträcker sig över flera partitioner, men varje läsa transaktioner är begränsade till en enda sida och hanteras från inom en enda partition.

## <a name="consistency-levels"></a>Konsekvensnivåer
Du kan konfigurera en standard-konsekvensnivå på ditt databaskonto som gäller för alla behållare (och databaser) under ditt Cosmos DB-konto. Använd standard-konsekvensnivå som anges på kontot som standard alla läsningar och frågor som har utfärdats för resurserna som användardefinierade. Kan du slappna av konsekvensnivå på en specifik Läs/fråga begäran med hjälp av i var och en API som stöds. Det finns fem typer av konsekvensnivåer som stöds av Azure Cosmos DB replikering protokollet och som ger ett tydligt samband mellan specifika konsekvensgarantier och prestanda, enligt beskrivningen i det här avsnittet.

<a id="strong"></a>
**Stark**: 

* Stark konsekvens erbjuder en [linjärbarhetsgaranti](https://aphyr.com/posts/313-strong-consistency-models) garanterar med läsningar garanterat returneras den senaste versionen av ett objekt. 
* Stark konsekvens garanterar att en skrivning endast är synliga när den överförs varaktigt majoritetskvorum av repliker. En skrivning synkront utförs antingen varaktigt av både den primära servern och kvorum av sekundärservrar eller den har avbrutits. Läs godkänns alltid av majoritetskvorum, en klient kan aldrig se en ogenomförda eller partiella skrivs och alltid är säkert att läsa den senaste godkända skrivningen. 
* Azure Cosmos DB-konton som är konfigurerade för att använda stark konsekvens kan inte associera mer än en Azure-region med ett Azure Cosmos DB-konto.  
* Kostnaden för en Läsåtgärd (i [programbegäran](request-units.md) förbrukas) med stark konsekvens är högre än session och slutlig, men samma som begränsad föråldring.

<a id="bounded-staleness"></a>
**Begränsad föråldring**: 

* Bunden föråldring konsekvensgarantier som läsningar kan släpar efter skrivningar med högst *K* versioner eller prefix för ett objekt eller *t* tidsintervallet. 
* Därför när välja begränsad föråldring, ”föråldring” kan konfigureras på två sätt: antal versioner *K* för objektet som läsningar släpar efter skrivningar och tidsintervallet *t* 
* Bunden föråldring erbjudanden totala globala ordning förutom i ”föråldring fönstret”. Monoton Läs garantier som finns inom en region både inuti och utanför ”föråldring fönstret”. 
* Begränsad föråldring tillhandahåller garanterad starkare konsekvens än session, konsekventa prefix eller eventuell konsekvens. För globalt distribuerade program rekommenderar vi du använder begränsad föråldring för scenarier där du vill ha stark konsekvens men vill också 99,99% tillgänglighet och svarstider.   
* Azure Cosmos DB-konton som är konfigurerade med begränsad föråldring, konsekvens kan associera valfritt antal Azure-regioner med sitt Azure Cosmos DB-konto. 
* Kostnaden för en Läsåtgärd (sett till antalet förbrukade ru) med begränsad föråldring är högre än session och slutlig konsekvens, men samma som stark konsekvens.

<a id="session"></a>
**Sessionen**: 

* Sessionskonsekvens är begränsad till en klientsession till skillnad från de globala konsekvensmodeller erbjuds av starka och begränsad föråldring konsekvensnivåer. 
* Sessionskonsekvens är perfekt för alla scenarier där en enhet eller användare session ingår eftersom det garanterar monotoniska läsningar, monotona skrivningar och Läs dina egna skrivningar (RYW) garanterar. 
* Sessionskonsekvens ger förutsägbar konsekvens för en session och maximalt läsningsdataflöde och erbjuder de lägsta svarstid skrivningar och läsningar. 
* Azure Cosmos DB-konton som har konfigurerats med sessionskonsekvens kan associera valfritt antal Azure-regioner med sitt Azure Cosmos DB-konto. 
* Kostnaden för en Läsåtgärd (sett till antalet förbrukade ru) är mindre än starka och begränsad föråldring, men mer än eventuell konsekvens med session konsekvensnivå.

<a id="consistent-prefix"></a>
**Konsekvent Prefix**: 

* Konsekvent prefix garanterar att eventuella ytterligare skrivningar saknas, replikerna inom gruppen så småningom att Konvergera. 
* Konsekvent prefix garanterar att läsningar aldrig ser skrivningar i oordning. Om skrivningar utfördes i ordningen `A, B, C`, och sedan en klient ser antingen `A`, `A,B`, eller `A,B,C`, men aldrig oordnade som `A,C` eller `B,A,C`.
* Azure Cosmos DB-konton som har konfigurerats med konsekvent prefix konsekvenskontroll kan associera valfritt antal Azure-regioner med sitt Azure Cosmos DB-konto. 

<a id="eventual"></a>
**Slutlig**: 

* Slutlig konsekvens garanterar att eventuella ytterligare skrivningar saknas, replikerna inom gruppen så småningom att Konvergera. 
* Slutlig konsekvens är den svagaste formen av konsekvens där en klient kan hämta de värden som är äldre än de som den hade läst.
* Slutlig konsekvens ger den svagaste läsningskontinuitet men erbjuder kortast svarstid för både läsningar och skrivningar.
* Azure Cosmos DB-konton som är konfigurerade med eventuell konsekvens kan associera valfritt antal Azure-regioner med sitt Azure Cosmos DB-konto. 
* Kostnaden för en Läsåtgärd (sett till antalet förbrukade ru) med slutlig konsekvens är det lägsta värdet för alla Azure Cosmos DB-konsekvensnivåer.

## <a name="configuring-the-default-consistency-level"></a>Konfigurera standard-konsekvensnivå
1. I den [Azure-portalen](https://portal.azure.com/), i Snabbåtkomstfältet klickar du på **Azure Cosmos DB**.
2. Markera det databaskonto du vill ändra på sidan **Azure Cosmos DB**.
3. På sidan klickar du på **Standardkonsekvens**.
4. I den **Standardkonsekvens** väljer du den nya konsekvensnivån och klicka på **spara**.
   
    ![Skärmbild som fokus på inställningsikonen och Standardkonsekvens post](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Konsekvensnivåer för frågor
Som standard för användardefinierade resurser är samma som konsekvensnivån för läsningar i konsekvensnivå för frågor. Som standard uppdateras indexet synkront på varje infoga, Ersätt eller borttagning av ett objekt till Cosmos DB-behållare. På så sätt kan frågor för att respektera samma konsekvensnivå som punkt läsningar. Azure Cosmos DB är skrivning optimerade och stöder varaktigt mängder skrivningar, synkron indexunderhåll och betjänar konsekventa frågor, kan du konfigurera vissa behållare för att uppdatera deras index lazily. Lazy indexering ytterligare förstärker Skrivprestandan och är perfekt för scenarier för inmatning av grupp när en arbetsbelastning är främst Läs omfattande.  

| Indexering läge | Läsningar | Frågor |
| --- | --- | --- |
| Konsekvens (standard) |Välj ett värde från stark, begränsad föråldring, session, enhetligt prefix eller eventuell |Välj ett värde från stark, begränsad föråldring, session, eller eventuell |
| Lazy |Välj ett värde från stark, begränsad föråldring, session, enhetligt prefix eller eventuell |Eventuell |
| Ingen |Välj ett värde från stark, begränsad föråldring, session, enhetligt prefix eller eventuell |Inte tillämpligt |

Med läsbegäranden, kan som sänka konsekvensnivå för en specifik fråga begäran i varje API: et.

## <a name="consistency-levels-for-the-mongodb-api"></a>Konsekvensnivåer för MongoDB-API

Azure Cosmos DB implementerar för närvarande MongoDB version 3.4, som har två konsekvensinställningar, starka och slutliga. Eftersom Azure Cosmos DB är multi-api, tillämpas konsekvensinställningarna på kontonivå och verkställandet av konsekvenskontrollen styrs av varje API.  Tills MongoDB 3.6 existerade inte begreppet sessionskonsekvens, så om du anger ett MongoDB API-konto för att använda sessionskonsekvens nedgraderas konsekvenskontrollen till eventuell när du använder MongoDB APIs. Om du behöver en garanti för läsa-egen-kod för ett MongoDB API-konto måste standardkonsekvensnivån för kontot vara stark eller begränsad föråldring.

## <a name="next-steps"></a>Nästa steg
Om du vill göra mer information om konsekvensnivåer och kompromisser rekommenderar vi följande resurser:

* [Replikerade datakonsekvens baseboll (video) av Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Replikerade datakonsekvens baseboll (White Paper) av Doug Terry](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Sessionen garantier för svagt konsekvent replikerade Data](http://dl.acm.org/citation.cfm?id=383631)
* [Konsekvens kompromisser i moderna distribuerade system databasdesign: Fästpunkten är bara en del av artikeln](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Avsnittet om sannolikhetsbunden begränsad föråldring (PBS) för praktiska partiella beslutsförhet](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* [Slutlig konsekvent – Revisited](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [Den belastning, kapacitet och tillgängligheten för kvorum system, SIAM journalen på databehandling](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Rad: en fullständig och automatisk linjärbarhetsgaranti bricka, åtal för 2010 ACM SIGPLAN konferens om programmering språk utformning och implementering](http://dl.acm.org/citation.cfm?id=1806634)
* [Probabilistically begränsad föråldring för praktiska partiella beslutsförhet](http://dl.acm.org/citation.cfm?id=2212359)
