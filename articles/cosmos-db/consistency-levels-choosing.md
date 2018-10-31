---
title: Välja rätt konsekvensnivå för ditt program | Microsoft Docs
description: Välja rätt konsekvensnivå för ditt program i Azure Cosmos DB.
keywords: konsekvens, prestanda, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 4b438320ac75918d10880a4bcdccadca4eebec32
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244187"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>Välja rätt konsekvens för ditt program

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, svarstider, eller båda, se grundläggande förhållandet mellan läsningskontinuitet jämfört med tillgänglighet, svarstid och dataflöde. De flesta kommersiellt distribuerade databaser Ställ utvecklare kan välja mellan två extrem konsekvensmodeller: stark konsekvens och slutlig konsekvens. Azure Cosmos DB kan utvecklare välja mellan fem väldefinierade konsekvensmodeller: stark, begränsad föråldring, session, enhetligt prefix och slutlig. Var och en av dessa konsekvensmodeller är väldefinierade, intuitivt och kan användas för den specifika verkliga scenarier. Var och en av de fem konsekvensmodeller ger tydliga tillgänglighet och prestanda kompromisser och backas upp av omfattande serviceavtal. Följande enkla överväganden hjälper dig att fatta rätt beslut i många vanliga scenarier.

## <a name="sql-api-or-table-api"></a>SQL-API eller tabell-API

- Sessionskonsekvens är den optimala för många verkliga scenarier, och det är det rekommendera alternativet. Mer information finns i [anvisningar: hantera sessionstoken för ditt program](how-to-manage-consistency.md#utilize-session-tokens).
- Om programmet kräver stark konsekvens, bör du använda begränsad föråldring konsekvensnivå.
- Om du behöver striktare konsekvensgarantier än de som tillhandahålls av sessionskonsekvens och enskild ensiffriga svarstid för skrivningar, rekommenderas du använder begränsad föråldring konsekvensnivå.  
- Om programmet kräver konsekvens, rekommenderas du använder konsekvent prefix konsekvensnivå.
- Om du behöver mindre strikta konsekvensgarantier än vad som tillhandahålls av sessionskonsekvens, rekommenderas du använder konsekvent prefix konsekvensnivå.
- Om du behöver högsta möjliga tillgänglighet och lägsta svarstid kan sedan använda slutlig konsekvensnivå.

## <a name="cassandra-mongodb-or-gremlin-api"></a>Cassandra, MongoDB eller Gremlin-API

- Mer information om mappning mellan ”Läs konsekvensnivå” av Apache Cassandra och Cosmos DB-konsekvensnivåer finns [konsekvensnivåer och Cosmos DB API: er](consistency-levels-across-apis.md#cassandra-mapping).
- Mer information om mappning mellan ”Läs problem” för MongoDB och Azure Cosmos DB-konsekvensnivåer finns [konsekvensnivåer och Cosmos DB API: er](consistency-levels-across-apis.md#mongo-mapping).

## <a name="you-may-get-stronger-consistency-guarantees-in-practice"></a>Du kan få bättre konsekvensgarantier i praktiken

Konsekvensgarantier för en Läsåtgärd motsvarar färskhet och sorteringen av tillståndet för databasen som begärs. Läs konsekvens är kopplad till beställning och spridningen av skrivåtgärder (uppdatera).  

När konsekvensnivån är inställd på **begränsad föråldring**, garanterar Cosmos DB att klienterna alltid läsa värdet för föregående skrivåtgärder, med en fördröjning som avgränsas av fönstret föråldring.

När konsekvensnivån är inställd på **stark**fönstret föråldring motsvarar noll och klienterna garanteras att läsa senaste allokerade värdet för skrivning.

Fönstret föråldring är i hög grad beroende på din arbetsbelastning för de återstående tre konsekvensnivåerna. Exempel: om det finns inga skrivningar händer på databasen, en Läsåtgärd med **slutlig**, **session**, eller **konsekvent prefix** konsekvensnivåer troligen kommer att ge samma resultat som en Läsåtgärd med stark konsekvensnivå.

Om ditt Cosmos DB-konto har konfigurerats med alla konsekvensnivå än stark konsekvens, kan du se sannolikheten för dina kunder om att hämta starkt konsekventa läsningar som (linearizable) för din workload(s) genom att titta på avsnittet om Sannolikhetsbunden bunden Föråldring (PBS) mått som exponeras i Azure-portalen [finns här hur du använder PBS-mått](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric). Avsnittet om sannolikhetsbunden begränsad föråldring visar hur eventuell är din slutlig konsekvens. Det här måttet ger en inblick i hur ofta du får en starkare konsekvens än konsekvensnivå som du har konfigurerat på ditt Cosmos DB-konto. Med andra ord kan du se sannolikheten för (visas i millisekunder) komma starkt konsekventa läsningar för en kombination av en Skriv- och läsregioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvensnivåer i följande artiklar:

* [Konsekvens mappning på i Cosmos DB API: er](consistency-levels-across-apis.md)
* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Så här hanterar du sessionstoken för ditt program](how-to-manage-consistency.md#utilize-session-tokens)
* [Så här övervakar du Probabilistically bunden föråldring (PBS)-mått](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)