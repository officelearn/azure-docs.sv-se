---
title: Välj rätt konsekvensnivå för din Azure Cosmos DB-app
description: Välja rätt konsekvensnivå för ditt program i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441925"
---
# <a name="choose-the-right-consistency-level"></a>Välja rätt konsekvensnivå 

Distribuerade databaser som är beroende av replikering för hög tillgänglighet, låg latens eller båda, gör den grundläggande avvägningen mellan läskonsekvens kontra tillgänglighet, svarstid och dataflöde. De flesta kommersiellt tillgängliga distribuerade databaser ber utvecklare att välja mellan de två extrema konsekvensmodellerna: *stark* konsekvens och *eventuell* konsekvens. Azure Cosmos DB gör det möjligt för utvecklare att välja bland de fem väldefinierade konsekvensmodellerna: *stark*, *begränsad föråldring,* *session,* *konsekvent prefix* och *eventuellt*. Var och en av dessa konsekvensmodeller är väldefinierade, intuitiva och kan användas för specifika verkliga scenarier. Var och en av de fem konsekvensmodellerna ger exakta [tillgänglighets- och prestandaavvägningar](consistency-levels-tradeoffs.md) och backas upp av omfattande SLA.Each of the five consistency models provide precise availability and performance tradeoffs and are backed by comprehensive SLAs. Följande enkla överväganden hjälper dig att göra rätt val i många vanliga scenarier.

## <a name="sql-api-and-table-api"></a>SQL API och tabell-API

Tänk på följande om ditt program är byggt med SQL API eller Tabell-API:

- För många verkliga scenarier är sessionskonsekvens optimal och det är det rekommenderade alternativet. Mer information finns i [Så här hanterar du sessionstoken för ditt program](how-to-manage-consistency.md#utilize-session-tokens).

- Om ditt program kräver stark konsekvens rekommenderar vi att du använder begränsad konsekvensnivå.

- Om du behöver striktare konsekvensgarantier än de som tillhandahålls av sessionskonsekvens och ensiffrig millisekundfördröjning för skrivningar, rekommenderar vi att du använder begränsad konsekvenskonsekvensnivå.  

- Om ditt program kräver slutlig konsekvens rekommenderar vi att du använder konsekvent prefixkonsekvensnivå.

- Om du behöver mindre strikta konsekvensgarantier än de som tillhandahålls av sessionskonsekvens, rekommenderar vi att du använder konsekvent prefixkonsekvensnivå.

- Om du behöver den högsta tillgängligheten och den lägsta svarstiden använder du den slutliga konsekvensnivån.

- Om du behöver ännu högre datahållbarhet utan att offra prestanda kan du skapa en anpassad konsekvensnivå på programlagret. Mer information finns [i Så här implementerar du anpassad synkronisering i dina program](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB och Gremlin API: er

- Mer information om hur du mappar mellan "Läs konsekvensnivå" som erbjuds i Apache Cassandra och Cosmos DB-konsekvensnivåer finns i [konsekvensnivåer och Cosmos DB API:er](consistency-levels-across-apis.md#cassandra-mapping).

- Mer information om mappning mellan "Läsproblem" för MongoDB- och Azure Cosmos DB-konsekvensnivåer finns i [konsekvensnivåer och Cosmos DB API:er](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Konsekvensgarantier i praktiken

I praktiken kan du ofta få starkare konsekvensgarantier. Konsekvensgarantier för en läsåtgärd motsvarar färskhet och ordning för databastillståndet som du begär. Läskonsekvens är knuten till beställning och spridning av skriv-/uppdateringsåtgärderna.  

* När konsekvensnivån är inställd **på begränsad föråldring**garanterar Cosmos DB att klienterna alltid läser värdet för en tidigare skrivning, med en fördröjning som avgränsas av inaktuella fönstret.

* När konsekvensnivån är inställd på **stark**är inaktuella fönstret motsvarande noll och klienterna är garanterade att läsa det senaste bekräftade värdet för skrivåtgärden.

* För de återstående tre konsekvensnivåerna är inaktuella fönster till stor del beroende av din arbetsbelastning. Om det till exempel inte finns några skrivåtgärder i databasen, kan en läsåtgärd med **eventuella**, **session-** eller **konsekventa prefixkonsekvensnivåer** ge samma resultat som en läsåtgärd med stark konsekvensnivå.

Om ditt Azure Cosmos-konto är konfigurerat med en annan konsekvensnivå än den starka konsekvensen kan du ta reda på sannolikheten för att dina klienter kan få starka och konsekventa läsningar för dina arbetsbelastningar genom att titta på pbs-måttet *(Probabilistically Bounded Staleness).* Det här måttet visas i Azure-portalen, om du vill veta mer, se [Övervaka probabilistically bounded staleness (PBS) mått](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic avgränsade staleness visar hur så småningom är din slutliga konsekvens. Det här måttet ger en inblick i hur ofta du kan få en starkare konsekvens än den konsekvensnivå som du för närvarande har konfigurerat på ditt Azure Cosmos-konto. Med andra ord kan du se sannolikheten (mätt i millisekunder) för att få starkt konsekventa läsningar för en kombination av skriv- och läsregioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvensnivåerna i följande artiklar:

* [Mappning på konsekvensnivå över Cosmos DB-API:er](consistency-levels-across-apis.md)
* [Tillgänglighets- och prestandaavvägningar för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Så här hanterar du sessionstoken för ditt program](how-to-manage-consistency.md#utilize-session-tokens)
* [Övervaka PBS-mått (probabilistiskt begränsad föråldring)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
