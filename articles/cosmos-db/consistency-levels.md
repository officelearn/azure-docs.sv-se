---
title: Konsekvensnivåer i Azure Cosmos DB
description: Azure Cosmos DB har fem konsekvensnivåer för att belastningsutjämna slutlig konsekvens, tillgänglighet och svarstid med de.
keywords: slutlig konsekvens, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 075f79707dda4683c288163ec7bfdbf9807e153f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834884"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsekvensnivåer i Azure Cosmos DB

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, svarstider, eller båda, se grundläggande förhållandet mellan läsningskontinuitet jämfört med tillgänglighet, svarstid och dataflöde. De flesta kommersiellt distribuerade databaser Ställ utvecklare kan välja mellan två extrem konsekvensmodeller: stark konsekvens och slutlig konsekvens. Den [linjärbarhetsgaranti](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) eller stark konsekvensmodell är data programmabilitys guld skyddsnivå. Men den lägger till en brant priset för högre svarstid (i stabilt tillstånd) och minskad tillgänglighet (vid fel). Å andra sidan, eventuell konsekvens ger högre tillgänglighet och bättre prestanda, men är svåra att programmerar program. 

Azure Cosmos DB närmar sig datakonsekvens som ett spektrum av alternativ i stället för två extremval. Stark konsekvens och slutlig konsekvens är placerade i slutet, men det finns många konsekvensval längs spektrumet. Utvecklare kan använda dessa alternativ för att se exakt val och detaljerade nackdelar med avseende på hög tillgänglighet eller prestanda. 

Med Azure Cosmos DB kan utvecklare välja mellan fem väldefinierade konsekvensmodeller på konsekvensspektrumet. Från starkast till svagast, modellerna är stark, begränsad föråldring, session, enhetligt prefix och slutlig. Modeller är väldefinierade och intuitivt. De kan användas för den specifika verkliga scenarier. Varje modellen ger [tillgänglighet och prestanda kompromisser](consistency-levels-tradeoffs.md) och backas upp av omfattande serviceavtal. Följande bild visar olika konsekvensnivåer som ett spektrum.

![Konsekvens som ett spektrum](./media/consistency-levels/five-consistency-levels.png)

Konsekvensnivåerna är regionsoberoende. Konsekvensnivå på ditt Azure Cosmos-konto är korrekt för alla läsåtgärder oavsett den region där läsningar och skrivningar behandlas, antalet regioner som associeras med ditt Azure Cosmos-konto, eller om ditt konto har konfigurerats med en en eller flera Skriv-regioner.

## <a name="scope-of-the-read-consistency"></a>Omfånget för läsningskontinuitet

Läs konsekvens gäller för en enda Läsåtgärd begränsade inom en partitionsnyckel område eller en logisk partition. Läsåtgärden kan utfärdas av en fjärransluten klient eller en lagrad procedur.

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

Du kan konfigurera standard-konsekvensnivå på ditt Azure Cosmos-konto när som helst. Standard-konsekvensnivå som konfigurerats på ditt konto gäller för alla Azure Cosmos DB-databaser och behållare med det kontot. Alla läsningar och frågor som utfärdas mot en behållare eller en databas kan du använda den angivna konsekvensnivån som standard. Mer information finns i så här [konfigurera standard-konsekvensnivå](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantier som är associerade med konsekvensnivåer

Omfattande serviceavtal som tillhandahålls av Azure Cosmos DB-garanti att 100% av läsbegäranden uppfyller konsekvens garanti för alla konsekvensnivå som du väljer. En läsbegäran uppfyller konsekvens serviceavtal om alla de konsekvensgarantier som är associerade med konsekvensnivån är uppfyllda. De exakta definitionerna av de fem konsekvensnivåerna i Azure Cosmos DB med hjälp av den [TLA + språket](http://lamport.azurewebsites.net/tla/tla.html) finns i den [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub-lagringsplatsen. 

Här beskrivs semantiken för de fem konsekvensnivåerna:

- **Stark**: stark konsekvens erbjuder en [linjärbarhetsgaranti](https://aphyr.com/posts/313-strong-consistency-models) garanterar. Läsningar garanterat returneras den senaste dedicerade versionen av ett objekt. En klient ser aldrig en ogenomförda eller partiella skrivs. Användare garanterat alltid att läsa den senaste allokerade skrivningen.

- **Begränsad föråldring**: läsningar är garanterade att respektera konsekvent prefix-garantin. Läsningar kan släpar efter skrivningar med högst ”K”-versioner (det vill säga ”uppdateringar”) av ett objekt eller efter ”t” tidsintervall. När du väljer begränsad föråldring kan ”föråldring” konfigureras på två sätt: 

  * Antal versioner (K) av objektet
  * Tidsintervall (t) som kan läsningar släpar efter skrivningar 

  Bunden föråldring erbjudanden totala globala ordning förutom i ”föråldring fönstret”. Monoton Läs garantier finns inom en region både i och utanför fönstret föråldring. Stark konsekvens har samma semantik som de som erbjuds av begränsad föråldring. Fönstret föråldring är lika med noll. Begränsad föråldring kallas också tid fördröjd linjärbarhetsgaranti. När en klient utför läsåtgärder inom en region som accepterar skrivningar, är garantier som begränsad föråldring, konsekvens identiska med de garantierna med stark konsekvens.

- **Sessionen**: läsningar är garanterade att respektera konsekvent prefix (förutsatt att en session med en ”författare”), monotoniska läsningar, garanterar Monoton skrivningar, Läs-dina-skrivningar och skrivning-följer-läsning. Sessionskonsekvens är begränsad till en klientsession.

- **Konsekvent prefix**: uppdateringar som returneras innehåller något prefix av alla uppdateringar, utan några mellanrum. Konsekvent prefix garanterar att läsningar aldrig ser skrivningar out ordning.

- **Slutlig**: det finns ingen skrivordning garanti för läsningar. Om eventuella ytterligare skrivningar konvergerar replikerna så småningom.

## <a name="consistency-levels-explained-through-baseball"></a>Konsekvensnivåer baseboll

Låt oss ta ett spel baseboll-scenario som exempel. Föreställ dig en sekvens med skrivningar som representerar in resultatet från en baseboll-spel. Resultat för inning av inning beskrivs i den [replikerade datakonsekvens med basket](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) dokumentet. Den här hypotetiska basket spelet är för närvarande mitt sjunde inning. Är det sjunde--inning stretch. Besökare är bakom med ett värde av 2 till 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Körningar** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Besökare** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Startsida** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

En Azure Cosmos DB-behållare innehåller besökarna och home team som kör summor. När spelet pågår, läsa olika garantier kan leda till klienter som läser olika resultat. I följande tabell visas en fullständig uppsättning med resultat som kan returneras genom att läsa besökarnas och home poäng med var och en av de fem konsekvensgarantier. Den besökare poäng visas först. Olika möjliga returvärden avgränsas med kommatecken.

| **Konsekvensnivå** | **Poäng** |
| - | - |
| **Stark** | 2 – 5 |
| **Begränsad föråldring** | Resultat som innehåller högst en inning inaktuell: 2 – 3, 2 – 4, 2 – 5 |
| **Sessionen** | <ul><li>För writer: 2 – 5</li><li> för alla förutom skrivar: 0-0, 0 – 1, 0-2, 0-3, 0-4, 0 och 5, 1 – 0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>När du har läst 1-3: 1-3, 1 – 4, 1-5, 2-3, 2-4, 2 – 5</li> |
| **Konsekvent prefix** | 0-0, 0-1, 1-1, 1 – 2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Slutlig** | 0-0, 0 – 1, 0-2, 0-3, 0-4, 0 och 5, 1 – 0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Ytterligare resurser

Mer information om konsekvens begrepp att läsa följande artiklar:

- [Övergripande TLA + specifikationerna för de fem konsekvensnivåerna som Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikerade Data konsekvens förklaras via basket (video) av Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikerade Data konsekvens förklaras via basket (White Paper) av Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sessionen garantier för svagt konsekvent replikerade data](https://dl.acm.org/citation.cfm?id=383631)
- [Konsekvens kompromisser i moderna distribuerade system databasdesign: Fästpunkten är bara en del av artikeln](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Avsnittet om sannolikhetsbunden begränsad föråldring (PBS) för praktiska partiella beslutsförhet](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Så småningom konsekvent – Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konsekvensnivåer i Azure Cosmos DB kan du läsa följande artiklar:

* [Välja rätt konsekvens för ditt program](consistency-levels-choosing.md)
* [Konsekvensnivåer i Azure Cosmos DB API: er](consistency-levels-across-apis.md)
* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Konfigurera standard-konsekvensnivå](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Åsidosätt standard-konsekvensnivå](how-to-manage-consistency.md#override-the-default-consistency-level)

