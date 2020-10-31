---
title: Apache Cassandra och Azure Cosmos DB konsekvens nivåer
description: Apache Cassandra och Azure Cosmos DB konsekvens nivåer.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f640ad85fd34dd5a4803e7dd96f1c0283f0c859a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100440"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra och Azure Cosmos DB konsekvens nivåer
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Till skillnad från Azure Cosmos DB ger Apache Cassandra inte inbyggt exakt definierade konsekvens garantier. I stället ger Apache Cassandra en Skriv konsekvens nivå och en Läs konsekvens nivå för att möjliggöra hög tillgänglighet, konsekvens och latens fördröjningar. När du använder Azure Cosmos DB API för Cassandra:

* Den Skriv konsekvens nivån för Apache Cassandra mappas till standard konsekvens nivån som kon figurer ATS på ditt Azure Cosmos-konto. Konsekvens för en Skriv åtgärd (CL) kan inte ändras per begäran.

* Azure Cosmos DB mappar dynamiskt den Läs konsekvens nivå som anges av Cassandra-klient driv rutinen till en av Azure Cosmos DB konsekvens nivåer som kon figurer ATS dynamiskt på en läsbegäran.

## <a name="mapping-consistency-levels"></a>Mappning av konsekvensnivåer

I följande tabell visas hur de interna Cassandra-konsekvens nivåerna mappas till Azure Cosmos DBs konsekvens nivåer när du använder API för Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra konsekvens modell mappning" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Om ditt Azure Cosmos-konto har kon figurer ATS med en annan konsekvens nivå än den starka konsekvensen kan du ta reda på sannolikheten att dina klienter kan få starka och konsekventa läsningar för dina arbets belastningar genom att titta på Probabilistically-måttet ( *Bound* ). Det här måttet visas i Azure Portal. mer information finns i [övervaka Probabilistically-gränser för Inaktuellitet (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic-begränsad föråldrad visar hur den slutliga konsekvensen är. Det här måttet ger en inblick i hur ofta du kan få en bättre konsekvens än den konsekvens nivå som du för närvarande har konfigurerat på ditt Azure Cosmos-konto. Med andra ord kan du se sannolikheten (mätt i millisekunder) för att få starkt konsekventa läsningar för en kombination av Skriv-och Läs regioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om global distribution och konsekvens nivåer för Azure Cosmos DB:

* [Översikt av global distribution](distribute-data-globally.md)
* [Översikt över konsekvens nivå](consistency-levels.md)
* [Hög tillgänglighet](high-availability.md)
