---
title: Konsekvensnivåer i Azure Cosmos DB
description: Azure Cosmos DB har fem konsekvensnivåer för att balansera eventuell konsekvens, tillgänglighet och svarstider kompromisser.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4e3d29471064616039bf946bb2762c15ce67bf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530265"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsekvensnivåer i Azure Cosmos DB

Distribuerade databaser som är beroende av replikering för hög tillgänglighet, låg latens eller båda, gör den grundläggande avvägningen mellan läskonsekvens kontra tillgänglighet, svarstid och dataflöde. De flesta kommersiellt tillgängliga distribuerade databaser ber utvecklare att välja mellan de två extrema konsekvensmodellerna: *stark* konsekvens och *eventuell* konsekvens. Den linjära eller den starka konsekvens modellen är den gyllene standarden för data programmerbarhet. Men det lägger till ett pris för högre latens (i stabilt tillstånd) och minskad tillgänglighet (under fel). Å andra sidan erbjuder eventuell konsekvens högre tillgänglighet och bättre prestanda, men gör det svårt att programmera program. 

Azure Cosmos DB närmar sig datakonsekvens som ett spektrum av val i stället för två ytterligheter. Stark konsekvens och eventuell konsekvens är i slutet av spektrumet, men det finns många konsekvens val längs spektrumet. Utvecklare kan använda dessa alternativ för att göra exakta val och detaljerade kompromisser med avseende på hög tillgänglighet och prestanda. 

Med Azure Cosmos DB kan utvecklare välja mellan fem väldefinierade konsekvensmodeller på konsekvensspektrumet. Från starkast till mer avslappnad, modellerna inkluderar *stark,* *begränsad föråldring,* *session,* *konsekvent prefix*, och *eventuell* konsekvens. Modellerna är väldefinierade och intuitiva och kan användas för specifika verkliga scenarier. Varje modell ger [tillgänglighet och prestanda kompromisser](consistency-levels-tradeoffs.md) och backas upp av SLA. Följande bild visar de olika konsekvensnivåerna som ett spektrum.

![Konsekvens som spektrum](./media/consistency-levels/five-consistency-levels.png)

Konsekvensnivåerna är regionagnostiker och garanteras för alla åtgärder oavsett från vilken region läsningar och skrivningar visas, antalet regioner som är associerade med ditt Azure Cosmos-konto eller om ditt konto är konfigurerat med ett enda eller flera skrivregioner.

## <a name="scope-of-the-read-consistency"></a>Omfattningen av läskonsekvensen

Läskonsekvens gäller för en enda läsåtgärd som omfattas av ett partitionsnyckelintervall eller en logisk partition. Läsåtgärden kan utfärdas av en fjärrklient eller en lagrad procedur.

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

Du kan när som helst konfigurera standardkonsekvensnivån på ditt Azure Cosmos-konto. Standardkonsekvensnivån som konfigurerats för ditt konto gäller för alla Azure Cosmos-databaser och behållare under det kontot. Alla läsningar och frågor som utfärdas mot en behållare eller en databas använder som standard den angivna konsekvensnivån. Mer information finns i hur du [konfigurerar standardkonsekvensnivån](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantier i samband med konsekvensnivåer

De omfattande SLA:erna som tillhandahålls av Azure Cosmos DB garanterar att 100 procent av läsbegärandena uppfyller konsekvensgarantin för alla konsekvensnivåer du väljer. En läsbegäran uppfyller konsekvenssasssocietyavtalet om alla konsekvensgarantier som är associerade med konsekvensnivån är uppfyllda. De exakta definitionerna av de fem konsekvensnivåerna i Azure Cosmos DB med hjälp av TLA+-specifikationsspråket finns i [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub-repo.

Semantiken i de fem konsekvensnivåerna beskrivs här:

- **Stark**: Stark konsekvens ger en linjäriserbarhetsgaranti. Linjäritet avser serveringsbegäranden samtidigt. Läsningarna är garanterade att returnera den senaste bekräftade versionen av ett objekt. En klient ser aldrig en obekräftad eller partiell skrivning. Användare är alltid garanterade att läsa den senaste engagerade skriva.

  Följande bild illustrerar den starka överensstämmelsen med noter. När data har skrivits till regionen "Västra USA 2" får du det senaste värdet när du läser data från andra regioner:

  ![video](media/consistency-levels/strong-consistency.gif)

- **Begränsad föråldring:** Läsningarna är garanterade att uppfylla den konsekventa prefixgarantin. Läsningarna kan släpa efter skrivningar av högst *"K"-versioner* (det vill säga "uppdateringar") av ett objekt eller med *"T"* tidsintervall. Med andra ord, när du väljer begränsad föråldring, kan "föråldring" konfigureras på två sätt: 

  * Antalet versioner *(K)* av artikeln
  * Tidsintervallet (*T*) med vilket läsningarna kan släpa efter skrivningar 

  Begränsad föråldring erbjuder total global ordning förutom i ”föråldringsfönstret”. De monotona läsgarantierna finns inom en region både inom och utanför unkenhetsfönstret. Stark konsekvens har samma semantik som den som erbjuds av avgränsad föråldring. Det inaktuella fönstret är lika med noll. Begränsad föråldring kallas också tidsfördröjd linjäritet. När en klient utför läsåtgärder inom en region som accepterar skrivningar, är de garantier som tillhandahålls av begränsad föråldringskonsekvens identiska med dessa garantier genom den starka konsekvensen.

  Begränsad föråldring väljs ofta av globalt distribuerade program som förväntar sig låga skrivtidsdämningar men som kräver total global ordergaranti. Begränsad staleness är bra för applikationer med gruppsamarbete och delning, lager ticker, publicera-prenumerera / köa etc. Följande bild illustrerar den avgränsade föråldringskonsekvensen med noter. När data har skrivits till regionen "Västra USA 2" läser regionerna "Östra USA 2" och "Östra Australien" det skriftliga värdet baserat på den konfigurerade maximala fördröjningstiden eller den maximala driften:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Session:** Inom en enda klient session läser garanterat att hedra konsekvent-prefix (förutsatt att en enda "författare" session), monotoniska läser, monotoniska skriver, läsa-din-skriver, och skriva-följer-läser garantier. Klienter utanför sessionen som utför skrivningar kommer att se slutlig konsekvens.

  Sessionskonsekvens är den allmänt använda konsekvensnivån för både en och den globala distribuerade program. Det ger skrivfördrevenligheter, tillgänglighet och läsflöde som är jämförbart med den slutliga konsekvensen, men ger också de konsekvensgarantier som passar behoven hos program som skrivits för att fungera inom ramen för en användare. Följande bild illustrerar sessionskonsekvensen med noter. Regionen "Västra USA 2" och regionerna "Östra USA 2" använder samma session (session A) så att de båda läser data samtidigt. Medan regionen "Australia East" använder "Session B" så tar den emot data senare men i samma ordning som skrivningar.

  ![video](media/consistency-levels/session-consistency.gif)

- **Konsekvent prefix:** Uppdateringar som returneras innehåller ett prefix av alla uppdateringar, utan luckor. Konsekvent prefix konsekvensnivå garanterar att läsa aldrig se out-of-order skriver.

  Om skrivningar utfördes i ordningen `A, B, C` ser klienten antingen `A`, `A,B` eller `A,B,C`, men aldrig i oordning, till exempel `A,C` eller `B,A,C`. Konsekvent prefix ger skrivfördäring, tillgänglighet och läsflöde som är jämförbart med den slutliga konsekvensen, men ger också ordergarantier som passar behoven i scenarier där ordning är viktigt. Följande bild illustrerar konsekvensprefixet konsekvens med noter. I alla regioner, läser aldrig se i oordning skriver:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Eventuell:** Det finns ingen beställningsgaranti för läsningar. I avsaknad av ytterligare skrivningar konvergerar replikerna så småningom.  
Slutlig konsekvens är den svagaste formen av konsekvens eftersom en klient kan läsa de värden som är äldre än de som den hade läst tidigare. Eventuell konsekvens är idealisk om programmet inte kräver några beställningsgarantier. Exempel är antal retweets, gilla-markeringar eller kommentarer som inte är gängade. Följande bild illustrerar den slutliga överensstämmelsen med noter.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Ytterligare läsning

Mer information om konsekvensbegrepp finns i följande artiklar:

- [TLA+-specifikationer på hög nivå för de fem konsekvensnivåer som erbjuds av Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikerade data konsekvens förklaras genom Baseball (video) av Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikerade data konsekvens förklaras genom Baseball (whitepaper) av Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sessionsgarantier för svagt konsekventa replikerade data](https://dl.acm.org/citation.cfm?id=383631)
- [Konsekvens kompromisser i modern distribuerad databas Systems Design: CAP är bara en del av storyn](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Avgränsad föråldring (PBS) för praktiska partiella kvorum](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Så småningom konsekvent - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Nästa steg

Mer information om konsekvensnivåer i Azure Cosmos DB finns i följande artiklar:

* [Välj rätt konsekvensnivå för ditt program](consistency-levels-choosing.md)
* [Konsekvensnivåer i Azure Cosmos DB-API:er](consistency-levels-across-apis.md)
* [Tillgänglighets- och prestandaavvägningar för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Konfigurera standardkonsekvensnivån](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Åsidosätta standardkonsekvensnivån](how-to-manage-consistency.md#override-the-default-consistency-level)

