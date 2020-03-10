---
title: Konsekvensnivåer i Azure Cosmos DB
description: Azure Cosmos DB har fem konsekvensnivåer för att belastningsutjämna slutlig konsekvens, tillgänglighet och svarstid med de.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: b5d9df7a0afa9b4270f0eff643e083e5bccfceb8
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933733"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsekvensnivåer i Azure Cosmos DB

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, svarstider, eller båda, se grundläggande förhållandet mellan läsningskontinuitet jämfört med tillgänglighet, svarstid och dataflöde. De flesta kommersiellt tillgängliga distribuerade databaser ber utvecklare att välja mellan de två extrema konsekvens modellerna: *stark* konsekvens och *eventuell* konsekvens. Linearizability eller en stark konsekvens modell är guld standarden för data programmering. Men det lägger till ett pris med högre latens (i stabilt tillstånd) och minskad tillgänglighet (under haverier). I övrigt erbjuder eventuell konsekvens bättre tillgänglighet och bättre prestanda, men gör det svårt att program mera. 

Azure Cosmos DB närmar sig datakonsekvens som ett spektrum av alternativ i stället för två extremval. Stark konsekvens och eventuell konsekvens är i slutet av spektrumet, men det finns många konsekvens alternativ längs spektrumet. Utvecklare kan använda dessa alternativ för att göra exakta val och detaljerad kompromisser med avseende på hög tillgänglighet och prestanda. 

Med Azure Cosmos DB kan utvecklare välja mellan fem väldefinierade konsekvensmodeller på konsekvensspektrumet. Från starkast till mer avslappnad, innehåller modellerna *stark*, *begränsad föråldrad*, *session*, *konsekvent prefix*och *eventuell* konsekvens. Modellerna är väl definierade och intuitiva och kan användas för vissa verkliga scenarier. Varje modell tillhandahåller [tillgänglighets-och prestanda kompromisser](consistency-levels-tradeoffs.md) och backas upp av service avtal. Följande bild visar olika konsekvens nivåer som ett spektrum.

![Konsekvens som ett spektrum](./media/consistency-levels/five-consistency-levels.png)

Konsekvens nivåerna är regions oberoende och garanteras för alla åtgärder oavsett vilken region läsningen och skrivningen betjänas, antalet regioner som associeras med ditt Azure Cosmos-konto eller om ditt konto har kon figurer ATS med ett enda eller flera Skriv regioner.

## <a name="scope-of-the-read-consistency"></a>Omfånget för läsningskontinuitet

Läs konsekvens gäller för en enda Läsåtgärd begränsade inom en partitionsnyckel område eller en logisk partition. Läsåtgärden kan utfärdas av en fjärransluten klient eller en lagrad procedur.

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

Du kan konfigurera standard-konsekvensnivå på ditt Azure Cosmos-konto när som helst. Standard konsekvens nivån som kon figurer ATS för ditt konto gäller för alla Azure Cosmos-databaser och behållare under det kontot. Alla läsningar och frågor som utfärdas mot en behållare eller en databas kan du använda den angivna konsekvensnivån som standard. Mer information finns i så här [konfigurerar du standard konsekvens nivån](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantier som är associerade med konsekvensnivåer

Omfattande serviceavtal som tillhandahålls av Azure Cosmos DB-garanti att 100% av läsbegäranden uppfyller konsekvens garanti för alla konsekvensnivå som du väljer. En läsbegäran uppfyller konsekvens serviceavtal om alla de konsekvensgarantier som är associerade med konsekvensnivån är uppfyllda. De exakta definitionerna av fem konsekvens nivåer i Azure Cosmos DB med språket TLA + Specification finns i [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub lagrings platsen.

Här beskrivs semantiken för de fem konsekvensnivåerna:

- **Stark**: stark konsekvens erbjuder en linearizability-garanti. Linearizability refererar till att betjäna begär Anden samtidigt. Läsningar garanterat returneras den senaste dedicerade versionen av ett objekt. En klient ser aldrig en ogenomförda eller partiella skrivs. Användare garanterat alltid att läsa den senaste allokerade skrivningen.

  Följande bild illustrerar en stark konsekvens med noter. När data har skrivits till regionen "USA, östra", får du det senaste värdet när du läser data från andra regioner:

  ![video](media/consistency-levels/strong-consistency.gif)

- **Begränsad föråldrad**: läsningarna garanteras för att respektera den konsekventa prefix-garantin. Läsningarna kan vänta efter skrivningar med de flesta *"K"* -versioner (det vill säga "uppdateringar") för ett objekt eller med *"T"* -tidsintervallet. Med andra ord kan "föråldrad" konfigureras på två sätt när du väljer begränsat inaktuellitet: 

  * Antalet versioner (*KB*) av objektet
  * Tidsintervallet (*T*) som läsningarna kan vara i vänte tiden bakom skrivningar 

  Bunden föråldring erbjudanden totala globala ordning förutom i ”föråldring fönstret”. Monoton Läs garantier finns inom en region både i och utanför fönstret föråldring. En stark konsekvens har samma semantik som den som erbjuds av den begränsade inaktuellaheten. Fönstret föråldring är lika med noll. Begränsad föråldring kallas också tid fördröjd linjärbarhetsgaranti. När en klient utför Läs åtgärder inom en region som godkänner skrivningar, är de garantier som tillhandahålls av den begränsade inkonsekventa konsekvensen identiska med dessa garantier med stark konsekvens.

  Begränsad inaktuellhet väljs ofta av globalt distribuerade program som förväntar sig låga Skriv fördröjningar men kräver total global order garanti. Begränsat föråldrat är bra för program som har grupp samarbete och delning, aktie kurser, publicera-prenumeration/kö osv. Följande bild illustrerar den begränsade inaktuella konsekvensen med noter. När data har skrivits till regionen "USA, östra", läser regionerna "västra USA" och "Australien, östra" det skrivna värdet baserat på den konfigurerade maximala fördröjnings tiden eller den maximala driften:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Session**: inom ett enda klient-sessions-läsningar garanteras att följa det konsekventa prefixet (förutsatt att en enda "skribent"-session), enkel färgs läsning, enkel färgs skrivning, Läs-och skriv åtgärder och läsnings garantier. Klienter utanför sessionen som utför skrivningar kommer att se eventuell konsekvens.

  Konsekvens av sessionen är den ofta använda konsekvens nivån för både en region och globalt distribuerade program. Det ger Skriv fördröjning, tillgänglighet och Läs data flöde som är jämförbar med eventuell konsekvens, men som även ger konsekvens garantier som passar de program som är skrivna för att köras i kontexten för en användare. Följande bild illustrerar konsekvensen i sessionen med noter. Regionen "västra USA" och "östra USA"-regionerna använder samma session (session A) så att båda läser data samtidigt. Regionen "Australien, östra" använder "session B", så den tar emot data en senare, men i samma ordning som skrivarna.

  ![video](media/consistency-levels/session-consistency.gif)

- **Konsekvent prefix**: uppdateringar som returneras innehåller vissa prefix för alla uppdateringar, utan luckor. Konsekvens nivå för konsekvent prefix garanterar att Read aldrig ser inloggade skrivningar.

  Om skrivningar utfördes i order `A, B, C`, ser klienten antingen `A`, `A,B`eller `A,B,C`, men inte i rätt ordning som `A,C` eller `B,A,C`. Konsekvent prefix ger Skriv fördröjningar, tillgänglighet och Läs data flöde som är jämförbara med den slutliga konsekvensen, men ger även order garantier som uppfyller behoven i scenarier där ordningen är viktig. Följande bild illustrerar konsekvens för konsekvens med noter. I alla regioner ser läsningarna aldrig ut ur ordning skrivningar:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Eventuell**: det finns ingen beställnings garanti för läsningar. Om eventuella ytterligare skrivningar konvergerar replikerna så småningom.  
Eventuell konsekvens är den svagaste typen av konsekvens eftersom en klient kan läsa värdena som är äldre än de som har lästs tidigare. Eventuell konsekvens är idealisk där programmet inte kräver några ordnings garantier. I exemplen ingår antalet retweetar, gillar eller icke-trådade kommentarer. Följande bild illustrerar den slutliga konsekvensen med noter.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Ytterligare resurser

Mer information om konsekvens begrepp att läsa följande artiklar:

- [Hög nivå TLA + specifikationer för de fem konsekvens nivåerna som erbjuds av Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikerad data konsekvens förklaras genom baseboll (video) av Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikerad data konsekvens förklaras genom baseboll (whitepaper) av Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sessioner garanterar svagt konsekvent replikerade data](https://dl.acm.org/citation.cfm?id=383631)
- [Konsekvens av konsekvens i moderna distribuerade databas system design: CAP är bara en del av berättelsen](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic-begränsad föråldrad (PBS) för praktiska delar av kvorum](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventuellt konsekvent återbesökning](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konsekvensnivåer i Azure Cosmos DB kan du läsa följande artiklar:

* [Välj rätt konsekvens nivå för ditt program](consistency-levels-choosing.md)
* [Konsekvens nivåer i Azure Cosmos DB API: er](consistency-levels-across-apis.md)
* [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](consistency-levels-tradeoffs.md)
* [Konfigurera standard konsekvens nivån](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Åsidosätt standard konsekvens nivån](how-to-manage-consistency.md#override-the-default-consistency-level)

