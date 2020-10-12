---
title: Välj rätt konsekvens nivå för din Azure Cosmos DB-app
description: Välja rätt konsekvens nivå för ditt program i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 0a79f6883a150cb95724a7be30dcbd2e8e03f01f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396084"
---
# <a name="how-to-choose-the-right-consistency-level-for-your-azure-cosmos-db-application"></a>Hur väljer du rätt konsekvens nivå för ditt Azure Cosmos DB program? 

Distribuerade databaser förlitar sig på replikering för hög tillgänglighet, låg latens eller båda, och gör den grundläggande kompromissen mellan Läs konsekvens jämfört med tillgänglighet, svars tid och data flöde. De flesta kommersiellt tillgängliga distribuerade databaser ber utvecklare att välja mellan de två extrema konsekvens modellerna: *stark* konsekvens och *eventuell* konsekvens. Azure Cosmos DB gör det möjligt för utvecklare att välja bland de fem väldefinierade konsekvens modellerna: *stark*, *begränsad föråldrad*, *session*, *konsekvent prefix* och *eventuell*. Var och en av dessa konsekvens modeller är väldefinierad, intuitiv och kan användas för vissa verkliga scenarier. Var och en av de fem konsekvens modellerna ger exakta [tillgänglighets-och prestanda kompromisser](consistency-levels-tradeoffs.md) och backas upp av omfattande service avtal. Du kan konfigurera en standard konsekvens på konto nivån och [åsidosätta den på begär ande nivå](how-to-manage-consistency.md#override-the-default-consistency-level). Följande enkla saker hjälper dig att fatta rätt val i många vanliga scenarier.

## <a name="sql-api-and-table-api"></a>SQL API och Tabell-API

Tänk på följande när ditt program har skapats med SQL API eller Tabell-API:

- I många verkliga scenarier är konsekvensen av sessionen optimala och det är det rekommenderade alternativet. Mer information finns i, [hur man hanterar sessionstoken för ditt program](how-to-manage-consistency.md#utilize-session-tokens).

- Om ditt program kräver stark konsekvens rekommenderar vi att du använder den begränsade inaktuella konsekvens nivån.

- Om du behöver striktare konsekvens garantier än de som tillhandahålls av konsekvensen för sessioner och ensiffriga millisekunder för skrivningar, rekommenderar vi att du använder begränsad föråldrad konsekvens nivå.  

- Om programmet kräver eventuell konsekvens, rekommenderar vi att du använder konsekvent konsekvens nivå för prefix.

- Om du behöver mindre strikta konsekvens garantier än de som tillhandahålls av konsekvensen i sessionen, rekommenderar vi att du använder konsekvent konsekvens nivå för prefix.

- Om du behöver högsta tillgänglighet och lägsta latens kan du använda en eventuell konsekvens nivå.

- Om du behöver ännu högre data hållbarhet utan att offra prestanda kan du skapa en anpassad konsekvens nivå på program nivån. Mer information finns i [så här implementerar du anpassad synkronisering i dina program](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra-, MongoDB-och Gremlin-API: er

- Mer information om mappning mellan "Läs konsekvens nivå" som erbjuds i Apache Cassandra och Cosmos DB konsekvens nivåer finns i [konsekvens nivåer och Cosmos DB API: er](consistency-levels-across-apis.md#cassandra-mapping).

- Mer information om mappning mellan "Läs oro" av MongoDB-och Azure Cosmos DB konsekvens nivåer finns i [konsekvens nivåer och Cosmos DB-API: er](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Konsekvens garantier i praktiken

I praktiken kan du ofta få bättre konsekvens garantier. Konsekvens garantier för en Läs åtgärd motsvarar aktualiteten och ordningen för det databas tillstånd som du begär. Läs-konsekvens är knutet till beställning och spridning av Skriv-/uppdaterings åtgärder.  

* När konsekvens nivån är inställd på **gräns för inaktuellitet**, Cosmos DB garantera att klienterna alltid läser värdet för en tidigare skrivning, med en fördröjning som har bundits av inaktuella fönster.

* När konsekvens nivån är inställd på **stark**motsvarar inaktuellas fönstret noll, och klienterna garanteras att läsa det senaste allokerade värdet för Skriv åtgärden.

* För de återstående tre konsekvens nivåerna är inaktuella fönster i stort sett beroende av arbets belastningen. Om det till exempel inte finns några Skriv åtgärder i databasen, är det troligt att en Läs **åtgärd med konsekvens nivåer för körning**, **session**eller **konsekvent prefix** ger samma resultat som en Läs åtgärd med stark konsekvens nivå.

Om ditt Azure Cosmos-konto har kon figurer ATS med en annan konsekvens nivå än den starka konsekvensen kan du ta reda på sannolikheten att dina klienter kan få starka och konsekventa läsningar för dina arbets belastningar genom att titta på Probabilistically-måttet ( *Bound* ). Det här måttet visas i Azure Portal. mer information finns i [övervaka Probabilistically-gränser för Inaktuellitet (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic-begränsad föråldrad visar hur den slutliga konsekvensen är. Det här måttet ger en inblick i hur ofta du kan få en bättre konsekvens än den konsekvens nivå som du för närvarande har konfigurerat på ditt Azure Cosmos-konto. Med andra ord kan du se sannolikheten (mätt i millisekunder) för att få starkt konsekventa läsningar för en kombination av Skriv-och Läs regioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvens nivåer i följande artiklar:

* [Mappning av konsekvens nivå över Cosmos DB-API: er](consistency-levels-across-apis.md)
* [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](consistency-levels-tradeoffs.md)
* [Så här hanterar du sessionstoken för ditt program](how-to-manage-consistency.md#utilize-session-tokens)
* [Övervaka PBS-mått (probabilistiskt begränsad föråldring)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
