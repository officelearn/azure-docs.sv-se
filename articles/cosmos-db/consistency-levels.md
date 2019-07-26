---
title: Konsekvensnivåer i Azure Cosmos DB
description: Azure Cosmos DB har fem konsekvensnivåer för att belastningsutjämna slutlig konsekvens, tillgänglighet och svarstid med de.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 395b7bc31377fd771549a399032bad9d951ec804
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384922"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsekvensnivåer i Azure Cosmos DB

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, svarstider, eller båda, se grundläggande förhållandet mellan läsningskontinuitet jämfört med tillgänglighet, svarstid och dataflöde. De flesta kommersiellt tillgängliga distribuerade databaser ber utvecklare att välja mellan de två extrema konsekvens modellerna: *stark* konsekvens och *eventuell* konsekvens. Linearizability eller en stark konsekvens modell är guld standarden för data programmering. Men det lägger till ett pris med högre latens (i stabilt tillstånd) och minskad tillgänglighet (under haverier). I övrigt erbjuder eventuell konsekvens bättre tillgänglighet och bättre prestanda, men gör det svårt att program mera. 

Azure Cosmos DB närmar sig datakonsekvens som ett spektrum av alternativ i stället för två extremval. Stark konsekvens och eventuell konsekvens är i slutet av spektrumet, men det finns många konsekvens alternativ längs spektrumet. Utvecklare kan använda dessa alternativ för att göra exakta val och detaljerad kompromisser med avseende på hög tillgänglighet och prestanda. 

Med Azure Cosmos DB kan utvecklare välja mellan fem väldefinierade konsekvensmodeller på konsekvensspektrumet. Från starkast till mer avslappnad, innehåller modellerna *stark*, *begränsad*föråldrad, *session*, *konsekvent prefix*och *eventuell* konsekvens. Modellerna är väl definierade och intuitiva och kan användas för vissa verkliga scenarier. Varje modell tillhandahåller [tillgänglighets-och prestanda kompromisser](consistency-levels-tradeoffs.md) och backas upp av service avtal. Följande bild visar olika konsekvens nivåer som ett spektrum.

![Konsekvens som ett spektrum](./media/consistency-levels/five-consistency-levels.png)

Konsekvens nivåerna är regions oberoende och garanteras för alla åtgärder oavsett vilken region läsningen och skrivningen betjänas, antalet regioner som associeras med ditt Azure Cosmos-konto eller om ditt konto har kon figurer ATS med ett enda eller flera Skriv regioner.

## <a name="scope-of-the-read-consistency"></a>Omfånget för läsningskontinuitet

Läs konsekvens gäller för en enda Läsåtgärd begränsade inom en partitionsnyckel område eller en logisk partition. Läsåtgärden kan utfärdas av en fjärransluten klient eller en lagrad procedur.

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

Du kan konfigurera standard-konsekvensnivå på ditt Azure Cosmos-konto när som helst. Standard konsekvens nivån som kon figurer ATS för ditt konto gäller för alla Azure Cosmos-databaser och behållare under det kontot. Alla läsningar och frågor som utfärdas mot en behållare eller en databas kan du använda den angivna konsekvensnivån som standard. Mer information finns i så här [konfigurera standard-konsekvensnivå](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantier som är associerade med konsekvensnivåer

Omfattande serviceavtal som tillhandahålls av Azure Cosmos DB-garanti att 100% av läsbegäranden uppfyller konsekvens garanti för alla konsekvensnivå som du väljer. En läsbegäran uppfyller konsekvens serviceavtal om alla de konsekvensgarantier som är associerade med konsekvensnivån är uppfyllda. De exakta definitionerna av fem konsekvens nivåer i Azure Cosmos DB med språket TLA + Specification finns i [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub lagrings platsen.

Här beskrivs semantiken för de fem konsekvensnivåerna:

- **Stark**: Stark konsekvens erbjuder en linearizability-garanti. Linearizability refererar till att betjäna begär Anden samtidigt. Läsningar garanterat returneras den senaste dedicerade versionen av ett objekt. En klient ser aldrig en ogenomförda eller partiella skrivs. Användare garanterat alltid att läsa den senaste allokerade skrivningen.

- **Begränsad**föråldrad: Läsningarna garanteras för att respektera den konsekventa prefix-garantin. Läsningarna kan vara sena efter skrivningar med de flesta *"K"* -versioner (d.v.s. uppdateringar) av ett objekt eller med ett *"T"* -tidsintervall. Med andra ord kan "föråldrad" konfigureras på två sätt när du väljer begränsat inaktuellitet: 

  * Antalet versioner (*KB*) av objektet
  * Tidsintervallet (*T*) som läsningarna kan vara i vänte tiden bakom skrivningar 

  Bunden föråldring erbjudanden totala globala ordning förutom i ”föråldring fönstret”. Monoton Läs garantier finns inom en region både i och utanför fönstret föråldring. En stark konsekvens har samma semantik som den som erbjuds av den begränsade inaktuellaheten. Fönstret föråldring är lika med noll. Begränsad föråldring kallas också tid fördröjd linjärbarhetsgaranti. När en klient utför Läs åtgärder inom en region som godkänner skrivningar, är de garantier som tillhandahålls av den begränsade inkonsekventa konsekvensen identiska med dessa garantier med stark konsekvens.

- **Session**:  Inom en enda klient session är läsningar garanterade att följa det konsekventa prefixet (under förutsättning att en enda "skribent"-session), enkel färgs läsningar, enkla Skriv åtgärder, skriv-och skriv åtgärder-Läs garantier. Klienter utanför sessionen som utför skrivningar kommer att se eventuell konsekvens.

- **Konsekvent prefix**: Uppdateringar som returneras innehåller vissa prefix för alla uppdateringar, utan luckor. Konsekvent konsekvens nivå för prefix garanterar att läsning aldrig ser inloggade skrivningar.

- **Eventuell**: Det finns ingen beställnings garanti för läsningar. Om eventuella ytterligare skrivningar konvergerar replikerna så småningom.

## <a name="consistency-levels-explained-through-baseball"></a>Konsekvensnivåer baseboll

Låt oss ta ett spel baseboll-scenario som exempel. Föreställ dig en sekvens med skrivningar som representerar in resultatet från en baseboll-spel. Resultat för inning av inning beskrivs i den [replikerade datakonsekvens med basket](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) dokumentet. Den här hypotetiska basket spelet är för närvarande mitt sjunde inning. Är det sjunde--inning stretch. Besökarna har en poäng på mellan 2 och 5 som visas nedan:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Körningar** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Besökare** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Startsida** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

En Azure Cosmos-behållare innehåller körnings summorna för besökare och hem team. När spelet pågår, läsa olika garantier kan leda till klienter som läser olika resultat. I följande tabell visas en fullständig uppsättning med resultat som kan returneras genom att läsa besökarnas och home poäng med var och en av de fem konsekvensgarantier. Den besökare poäng visas först. Olika möjliga returvärden avgränsas med kommatecken.

| **Konsekvensnivå** | **Poäng (besökare, hem)** |
| - | - |
| **Stark** | 2 – 5 |
| **Begränsad föråldring** | Poäng som är högst en inning inaktuell: 2-3, 2-4, 2-5 |
| **Sessionen** | <ul><li>För skrivaren: 2 – 5</li><li> För någon annan än skrivaren: 0-0, 0 – 1, 0-2, 0-3, 0-4, 0 och 5, 1 – 0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>Efter läsning 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Konsekvent prefix** | 0-0, 0-1, 1-1, 1 – 2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Slutlig** | 0-0, 0 – 1, 0-2, 0-3, 0-4, 0 och 5, 1 – 0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Ytterligare resurser

Mer information om konsekvens begrepp att läsa följande artiklar:

- [Övergripande TLA + specifikationerna för de fem konsekvensnivåerna som Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikerade Data konsekvens förklaras via basket (video) av Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikerade Data konsekvens förklaras via basket (White Paper) av Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sessionen garantier för svagt konsekvent replikerade data](https://dl.acm.org/citation.cfm?id=383631)
- [Design av konsekvens i moderna distribuerade databas system: CAP är bara en del av berättelsen](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Avsnittet om sannolikhetsbunden begränsad föråldring (PBS) för praktiska partiella beslutsförhet](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Så småningom konsekvent – Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konsekvensnivåer i Azure Cosmos DB kan du läsa följande artiklar:

* [Välja rätt konsekvens för ditt program](consistency-levels-choosing.md)
* [Konsekvensnivåer i Azure Cosmos DB API: er](consistency-levels-across-apis.md)
* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Konfigurera standard-konsekvensnivå](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Åsidosätt standard-konsekvensnivå](how-to-manage-consistency.md#override-the-default-consistency-level)

