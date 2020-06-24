---
title: Konsekvens nivåer i Azure Cosmos DB
description: Azure Cosmos DB har fem konsekvens nivåer som gör det enklare att balansera eventuell konsekvens, tillgänglighet och svars tid.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 5b80a0b03959ec3fba90ed21e291c1fd021916c2
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119298"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsekvens nivåer i Azure Cosmos DB

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, låg latens eller båda, gör den grundläggande kompromissen mellan Läs konsekvens jämfört med tillgänglighet, svars tid och data flöde. De flesta kommersiellt tillgängliga distribuerade databaser ber utvecklare att välja mellan de två extrema konsekvens modellerna: *stark* konsekvens och *eventuell* konsekvens. Linearizability för en stark konsekvens modell är guld standarden för data programmering. Men det lägger till ett pris med högre Skriv fördröjning (i stabilt tillstånd) och minskad tillgänglighet (under haverier). I övrigt erbjuder eventuell konsekvens bättre tillgänglighet och bättre prestanda, men gör det svårt att program mera.

Azure Cosmos DB närmar sig data konsekvens som ett spektrum av alternativ i stället för två extrema. Utvecklare kan använda dessa alternativ för att göra exakta val och detaljerad kompromisser med avseende på hög tillgänglighet och prestanda.

Med Azure Cosmos DB kan utvecklare välja mellan fem väldefinierade konsekvens nivåer i konsekvens spektrumet. Dessa nivåer omfattar *stark*, *begränsad föråldrad*, *session*, *konsekvent prefix*och *eventuell* konsekvens. Nivåerna är väl definierade och intuitiva och kan användas för vissa verkliga scenarier. Varje nivå ger till [gång till tillgänglighets-och prestanda kompromisser](consistency-levels-tradeoffs.md) och backas upp av service avtal. Följande bild visar olika konsekvens nivåer som ett spektrum.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Konsekvent som ett spektrum" border="false" :::

Konsekvens nivåerna är regions oberoende och garanteras för alla åtgärder oavsett vilken region läsningen och skrivningen betjänas, antalet regioner som associeras med ditt Azure Cosmos-konto eller om ditt konto har kon figurer ATS med en eller flera Skriv regioner.

## <a name="scope-of-the-read-consistency"></a>Omfattningen av Läs konsekvensen

Läs konsekvens gäller för en enskild Läs åtgärd som är begränsad i en logisk partition. Läs åtgärden kan utfärdas av en fjärran sluten klient eller en lagrad procedur.

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

Du kan när som helst konfigurera standard konsekvens nivån för ditt Azure Cosmos-konto. Standard konsekvens nivån som kon figurer ATS för ditt konto gäller för alla Azure Cosmos-databaser och behållare under det kontot. Alla läsningar och frågor som utfärdats mot en behållare eller databas använder den angivna konsekvens nivån som standard. Mer information finns i så här [konfigurerar du standard konsekvens nivån](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantier kopplade till konsekvens nivåer

Den omfattande service avtal som tillhandahålls av Azure Cosmos DB garanterar att 100 procent av Läs begär Anden uppfyller konsekvens garantin för den konsekvens nivå du väljer. En läsbegäran uppfyller konsekvens avtalet om alla konsekvens garantier som är kopplade till konsekvens nivån är uppfyllda. De exakta definitionerna av fem konsekvens nivåer i Azure Cosmos DB med språket TLA + Specification finns i [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub lagrings platsen.

Semantiken för de fem konsekvens nivåerna beskrivs här:

- **Stark**: stark konsekvens erbjuder en linearizability-garanti. Linearizability refererar till att betjäna begär Anden samtidigt. Läsningarna garanterar att du returnerar den senaste allokerade versionen av ett objekt. En klient ser aldrig en obekräftad eller delvis skrivning. Användare är alltid garanterade att läsa den senaste dedikerade skrivningen.

  Följande bild illustrerar en stark konsekvens med noter. När data har skrivits till regionen "USA, västra 2" och du läser data från andra regioner får du det senaste värdet:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="video":::

- **Begränsad föråldrad**: läsningarna garanteras för att respektera den konsekventa prefix-garantin. Läsningarna kan vara sena efter skrivningar med de flesta *"K"* -versioner (det vill säga "uppdateringar") för ett objekt eller med *"T"* tidsintervall, beroende på vilken som uppnås först. Med andra ord kan "föråldrad" konfigureras på två sätt när du väljer begränsat inaktuellitet:

- Antalet versioner (*KB*) av objektet
- Tidsintervallet (*T*) som läsningarna kan vara i vänte tiden bakom skrivningar

Den begränsade inaktuella inaktuellheten ger total global ordning utanför inaktuella inaktuella fönster. När en klient utför Läs åtgärder inom en region som godkänner skrivningar, är de garantier som tillhandahålls av den begränsade inkonsekventa konsekvensen identiska med dessa garantier med stark konsekvens.

I inaktuella fönster har den begränsade inaktuellaheten följande konsekvens garantier:

- Konsekvens för klienter i samma region för ett enda huvud konto = stark
- Konsekvens för klienter i olika regioner för ett enda huvud konto = konsekvent prefix
- Konsekvens för klienter som skriver till en enda region för ett multi-master-konto = konsekvent prefix
- Konsekvens för klienter som skriver till olika regioner för flera huvud konton = eventuell

  Begränsad inaktuellhet väljs ofta av globalt distribuerade program som förväntar sig låga Skriv fördröjningar men kräver total global order garanti. Begränsat föråldrat är bra för program som har grupp samarbete och delning, aktie kurser, publicera-prenumeration/kö osv. Följande bild illustrerar den begränsade inaktuella konsekvensen med noter. Efter att data har skrivits till regionen "USA, västra 2", läser regionerna "USA, östra 2" och "Australien, östra" det skrivna värdet baserat på den konfigurerade maximala fördröjnings tiden eller maximalt antal åtgärder:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="video":::

- **Session**: inom ett enda klient-sessions-läsningar garanteras att det följer konsekventa prefix, enkla och enkla Skriv åtgärder, enkla Skriv åtgärder, säkerhets garantier för Skriv åtgärder. Detta förutsätter en enskild "skribent"-session eller delar sessionstoken för flera skrivare.

Klienter utanför sessionen som utför skrivningar får följande garantier:

- Konsekvens för klienter i samma region för ett enda huvud konto = konsekvent prefix
- Konsekvens för klienter i olika regioner för ett enda huvud konto = konsekvent prefix
- Konsekvens för klienter som skriver till en enda region för ett multi-master-konto = konsekvent prefix
- Konsekvens för klienter som skriver till flera regioner för ett konto med flera huvud servrar = eventuell

  Konsekvens av sessionen är den mest använda konsekvens nivån för både en region och globalt distribuerade program. Det ger Skriv fördröjning, tillgänglighet och Läs data flöde som är jämförbar med eventuell konsekvens, men som även ger konsekvens garantier som passar de program som är skrivna för att köras i kontexten för en användare. Följande bild illustrerar konsekvensen i sessionen med noter. "Västra USA 2 Writer" och "västra USA 2-läsaren" använder samma session (session A) så att båda läser samma data samtidigt. Regionen "Australien, östra" använder "session B", så den tar emot data senare, men i samma ordning som skrivarna.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="video":::

- **Konsekvent prefix**: uppdateringar som returneras innehåller vissa prefix för alla uppdateringar, utan luckor. Konsekvent konsekvens nivå för prefix garanterar att läsning aldrig ser inloggade skrivningar.

Om skrivningar utfördes i beställningen `A, B, C` ser en klient antingen `A` , `A,B` , eller `A,B,C` , men inte i ordning-permutationer som `A,C` eller `B,A,C` . Konsekvent prefix ger Skriv fördröjningar, tillgänglighet och Läs data flöde som är jämförbara med den slutliga konsekvensen, men ger även order garantier som uppfyller behoven i scenarier där ordningen är viktig. 

Nedan visas konsekvens garantier för konsekvent prefix:

- Konsekvens för klienter i samma region för ett enda huvud konto = konsekvent prefix
- Konsekvens för klienter i olika regioner för ett enda huvud konto = konsekvent prefix
- Konsekvens för klienter som skriver till en enda region för ett multi-master-konto = konsekvent prefix
- Konsekvens för klienter som skriver till flera regioner för ett konto med flera huvud servrar = eventuell

Följande bild illustrerar konsekvens för konsekvens med noter. I alla regioner ser läsningarna aldrig ut ur ordning skrivningar:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="video":::

- **Eventuell**: det finns ingen beställnings garanti för läsningar. I avsaknad av eventuella ytterligare skrivningar konvergerar replikerna slutligen.  
Eventuell konsekvens är den svagaste typen av konsekvens eftersom en klient kan läsa värdena som är äldre än de som har lästs tidigare. Eventuell konsekvens är idealisk där programmet inte kräver några ordnings garantier. I exemplen ingår antalet retweetar, gillar eller icke-trådade kommentarer. Följande bild illustrerar den slutliga konsekvensen med noter.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="video":::

## <a name="additional-reading"></a>Ytterligare läsning

Läs mer om konsekvens koncept i följande artiklar:

- [Hög nivå TLA + specifikationer för de fem konsekvens nivåerna som erbjuds av Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikerad data konsekvens förklaras genom baseboll (video) av Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikerad data konsekvens förklaras genom baseboll (whitepaper) av Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Sessioner garanterar svagt konsekvent replikerade data](https://dl.acm.org/citation.cfm?id=383631)
- [Konsekvens av konsekvens i moderna distribuerade databas system design: CAP är bara en del av berättelsen](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic-begränsad föråldrad (PBS) för praktiska delar av kvorum](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventuellt konsekvent återbesökning](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Nästa steg

Mer information om konsekvens nivåer i Azure Cosmos DB finns i följande artiklar:

- [Välj rätt konsekvens nivå för ditt program](consistency-levels-choosing.md)
- [Konsekvens nivåer i Azure Cosmos DB API: er](consistency-levels-across-apis.md)
- [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](consistency-levels-tradeoffs.md)
- [Konfigurera standardkonsekvensnivån](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Åsidosätta standardkonsekvensnivån](how-to-manage-consistency.md#override-the-default-consistency-level)
