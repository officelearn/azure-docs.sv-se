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
ms.openlocfilehash: 2611c25764503551c4da918d06bcaabe315cbf7c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963089"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsekvensnivåer i Azure Cosmos DB

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, svarstider, eller båda, se grundläggande förhållandet mellan läsningskontinuitet jämfört med tillgänglighet, svarstid och dataflöde. De flesta kommersiellt distribuerade databaser Ställ utvecklare kan välja mellan två extrem konsekvensmodeller: stark konsekvens och slutlig konsekvens. Medan den [linjärbarhetsgaranti](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) eller stark konsekvensmodell är guld-standard i data programmabilitys, läggs en brant priset för högre svarstid (i stabilt tillstånd) och minskad tillgänglighet (vid fel). Å andra sidan, eventuell konsekvens ger högre tillgänglighet och bättre prestanda, men är svåra att programmerar program.

Cosmos DB närmar sig datakonsekvens som ett spektrum av alternativ i stället för två extremval. Stark konsekvens och slutlig konsekvens är det två upphört att gälla å, finns men det många konsekvensval längs spektrumet. De här alternativen för konsekvenskontroll att utvecklare kan se exakt val och detaljerade nackdelar med avseende på hög tillgänglighet eller prestanda. Cosmos DB aktiverat utvecklare kan välja mellan fem väldefinierade konsekvensmodeller från konsekvensspektrumet (starkast till svagast) – **stark**, **begränsad föråldring**, **session** , **konsekvent prefix**, och **eventuell**. Var och en av dessa konsekvensmodeller är väldefinierade, intuitivt och kan användas för den specifika verkliga scenarier. Var och en av de fem konsekvensmodeller ger [tillgänglighet och prestanda kompromisser](consistency-levels-tradeoffs.md) och backas upp av omfattande serviceavtal.

![Konsekvens som ett spektrum](./media/consistency-levels/five-consistency-levels.png)

Konsekvensnivåerna är regionsoberoende. Konsekvensnivå på ditt Cosmos DB-konto är korrekt för alla läsåtgärder oavsett följande egenskaper:

- Regionen där läsningar och skrivningar behandlas
- Antalet regioner som associeras med ditt Cosmos-konto
- Om ditt konto har konfigurerats med en eller flera Skriv-regioner

## <a name="scope-of-the-read-consistency"></a>Omfånget för Läs-konsekvens

Läs-konsekvens gäller för en enda Läsåtgärd begränsade inom ett intervall av partitionsnyckel (det vill säga en logisk partition). Läsåtgärden kan utfärdas av en fjärransluten klient eller en lagrad procedur.

## <a name="configuring-the-default-consistency-level"></a>Konfigurera standard-konsekvensnivå

Du kan konfigurera den **standard konsekvensnivå** på ditt Cosmos DB-konto när som helst. Standard-konsekvensnivå som konfigurerats på ditt konto gäller för alla Cosmos-databaser (och behållare) med det kontot. Alla läsningar och frågor som utfärdas mot en behållare eller en databas använder den angivna konsekvensnivån som standard. Mer information finns i så här [konfigurera standard-konsekvensnivå](how-to-manage-consistency.md#configure-the-default-consistency-level) artikeln.

## <a name="guarantees-associated-with-consistency-levels"></a>Garantier som är associerade med konsekvensnivåer

Omfattande serviceavtal som tillhandahålls av Azure Cosmos DB-garanti att 100% av läsbegäranden uppfyller konsekvens garanti för alla konsekvensnivå som du väljer. En läsbegäran anses uppfylla konsekvens SLA, om de konsekvensgarantier som som är associerade med konsekvensnivån är uppfyllda. De exakta definitionerna av de fem konsekvensnivåerna i Cosmos DB med hjälp av den [TLA + språket](http://lamport.azurewebsites.net/tla/tla.html) finns i den [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub-lagringsplatsen. Semantiken för de fem konsekvensnivåerna beskrivs nedan:

- **Konsekvensnivå = ”strong”**: stark konsekvens erbjuder en [linjärbarhetsgaranti](https://aphyr.com/posts/313-strong-consistency-models) garanterar med läsningar garanterat returneras den senaste dedicerade versionen av ett objekt. En klient kommer aldrig att se en ogenomförda eller partiella skrivs. Användare garanterat alltid att läsa den senaste allokerade skrivningen.

- **Konsekvensnivå = ”begränsad föråldring”**: läsningar är garanterade att respektera konsekvent prefix-garantin. Läsningar kan släpar efter skrivningar med högst K-versioner (det vill säga ”uppdateringar” för ett objekt) eller genom att 't' tidsintervallet. När du väljer begränsad föråldring, kan ”föråldring” konfigureras på två sätt: 

  * Antal versioner (K) av objektet eller
  * Tidsintervallet (t) som kan läsningar släpar efter skrivningar. 

  Bunden föråldring erbjudanden totala globala ordning förutom i ”föråldring fönstret”. Monoton Läs garantier som finns inom en region både inuti och utanför ”föråldring fönstret”. Stark konsekvens har samma semantik som de som erbjuds av begränsad föråldring och med ett ”föråldring fönster” lika med noll. Begränsad föråldring också kallas **tid fördröjd linjärbarhetsgaranti**. När en klient utför läsåtgärder inom en region som accepterar skrivningar, är garantier som begränsad föråldring, konsekvens identiska med de med stark konsekvens.

- **Konsekvensnivå = ”session”**: läsningar garanterat respektera konsekvens-prefix, monotoniska läsningar, monotona skrivningar, Läs-dina-skrivningar, skrivning-följer-läsning garanterar. Sessionskonsekvens är begränsad till en klientsession.

- **Konsekvensnivå = ”konsekvent prefix”**: uppdateringar som returneras innehåller något prefix av alla uppdateringar, utan några mellanrum. Konsekvent prefix vi garanterar att läsningar aldrig ser skrivningar i oordning.

- **Konsekvensnivå = ”eventuell”**: det finns ingen skrivordning garanti för läsningar. I avsaknad av eventuella ytterligare skrivningar konvergerar replikerna så småningom.

## <a name="consistency-levels-explained-through-baseball"></a>Konsekvensnivåer baseboll

Låt oss ta basket spel scenariot som exempel, imagine en sekvens med skrivningar som representerar in resultatet från en baseboll-spel med resultat inning av inning enligt beskrivningen i den [replikerade datakonsekvens med basket](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) dokumentet. Hypotetiskt basket spelet är för närvarande mitt sjunde inning (de mest sjunde--inning Sträck ut) och home-teamet är vann 2 – 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Körningar** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Besökare** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Startsida** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

En Cosmos DB-behållare innehåller besökare och home team kör summor. När spelet pågår, läsa olika garantier kan leda till klienter som läser olika resultat. I följande tabell visas en fullständig uppsättning med resultat som kan returneras genom att läsa besökare och home poäng med var och en av de fem konsekvensgarantier. Den besökare poäng visas först och olika möjliga returvärden avgränsas med kommatecken.

| **Konsekvensnivå** | **Poäng** |
| - | - |
| **Stark** | 2 – 5 |
| **Begränsad föråldring** | som är i de flesta en inning inaktuella ”2-3, 2 – 4, 2 – 5 |
| **Sessionen** | <ul><li>för writer ”2 – 5</li><li> för alla förutom skrivar: 0-0, 0 – 1, 0-2, 0-3, 0-4, 0 och 5, 1 – 0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>När du har läst 1-3: 1-3, 1 – 4, 1-5, 2-3, 2-4, 2 – 5</li> |
| **Konsekvent Prefix** | 0-0, 0-1, 1-1, 1 – 2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Slutlig** | 0-0, 0 – 1, 0-2, 0-3, 0-4, 0 och 5, 1 – 0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1-5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Ytterligare resurser

Mer information om konsekvens begrepp att läsa följande artiklar:

- [Övergripande TLA + specifikationerna för de fem konsekvensnivåerna som Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikerade Data konsekvens baseboll (video) av Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikerade Data konsekvens baseboll (White Paper) av Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sessionen garantier för svagt konsekvent replikerade Data](https://dl.acm.org/citation.cfm?id=383631)
- [Konsekvens kompromisser i moderna distribuerade system databasdesign: Fästpunkten är bara en del av artikeln](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Avsnittet om sannolikhetsbunden begränsad föråldring (PBS) för praktiska partiella beslutsförhet](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Så småningom konsekvent – Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konsekvensnivåer i Cosmos DB kan du läsa följande artiklar:

* [Välja rätt konsekvensnivå för ditt program](consistency-levels-choosing.md)
* [Konsekvensnivåer i Cosmos DB API: er](consistency-levels-across-apis.md)
* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Så här konfigurerar du standard-konsekvensnivå](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Åsidosätta standard-konsekvensnivå](how-to-manage-consistency.md#override-the-default-consistency-level)

