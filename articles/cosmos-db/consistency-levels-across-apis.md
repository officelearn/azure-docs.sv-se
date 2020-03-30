---
title: Konsekvensnivåer och API:er för Azure Cosmos DB
description: Förstå mappningen på konsekvensnivå mellan olika API:er i Azure Cosmos DB och Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131461"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsekvensnivåer och API:er för Azure Cosmos DB

Azure Cosmos DB ger inbyggt stöd för trådprotokollkompatibla API:er för populära databaser. Dessa inkluderar MongoDB, Apache Cassandra, Gremlin och Azure Table storage. Dessa databaser erbjuder inte exakt definierade konsekvensmodeller eller SLA-stödda garantier för konsekvensnivåerna. De tillhandahåller vanligtvis bara en delmängd av de fem konsekvensmodeller som erbjuds av Azure Cosmos DB. 

När du använder SQL API, Gremlin API och Tabell API används standardkonsekvensnivån som konfigurerats på Azure Cosmos-kontot. 

När du använder Cassandra API eller Azure Cosmos DB:s API för MongoDB får program en fullständig uppsättning konsekvensnivåer som erbjuds av Apache Cassandra respektive MongoDB, med ännu starkare konsekvens- och hållbarhetsgarantier. Det här dokumentet visar motsvarande Azure Cosmos DB-konsekvensnivåer för Apache Cassandra och MongoDB-konsekvensnivåer.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Mappning mellan Apache Cassandra och Azure Cosmos DB-konsekvensnivåer

Till skillnad från Azure Cosmos DB ger Apache Cassandra inte inbyggt exakt definierade konsekvensgarantier.  Apache Cassandra tillhandahåller i stället en skrivkonsekvensnivå och en läskonsekvensnivå för att aktivera kompromisser med hög tillgänglighet, konsekvens och latens. När du använder Azure Cosmos DB:s Cassandra API: 

* Skrivkonsekvensnivån för Apache Cassandra mappas till standardkonsekvensnivån som konfigurerats på ditt Azure Cosmos-konto. Konsekvens för en skrivåtgärd (CL) kan inte ändras per begäran.

* Azure Cosmos DB mappar dynamiskt den läskonsekvensnivå som anges av Cassandra-klientdrivrutinen till en av Azure Cosmos DB-konsekvensnivåerna som konfigurerats dynamiskt på en läsbegäran. 

Följande tabell visar hur de inbyggda Cassandra-konsekvensnivåerna mappas till Azure Cosmos DB:s konsekvensnivåer när Cassandra API används:  

[![Cassandra konsekvens modell kartläggning](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mappning mellan MongoDB- och Azure Cosmos DB-konsekvensnivåer

Till skillnad från Azure Cosmos DB ger den inbyggda MongoDB inte exakt definierade konsekvensgarantier. I stället tillåter infödda MongoDB användare att konfigurera följande konsekvensgarantier: ett skrivproblem, en läsbekymring och isMaster-direktivet - för att dirigera läsåtgärderna till antingen primära eller sekundära repliker för att uppnå önskad konsekvensnivå. 

När du använder Azure Cosmos DB:s API för MongoDB behandlar MongoDB-drivrutinen din skrivregion som den primära repliken och alla andra regioner läse repliken. Du kan välja vilken region som är associerad med ditt Azure Cosmos-konto som en primär replik. 

När du använder Azure Cosmos DB:s API för MongoDB:

* Skrivproblemet mappas till standardkonsekvensnivån som konfigurerats på ditt Azure Cosmos-konto.
 
* Azure Cosmos DB mappar dynamiskt läsproblemet som anges av MongoDB-klientdrivrutinen till en av Azure Cosmos DB-konsekvensnivåerna som har konfigurerats dynamiskt på en läsbegäran.  

* Du kan kommentera en specifik region som är associerad med ditt Azure Cosmos-konto som "Master" genom att göra regionen till den första skrivbara regionen. 

Följande tabell visar hur de inbyggda problem med MongoDB-skriv-/läsfrågorna mappas till azure Cosmos-konsekvensnivåerna när du använder Azure Cosmos DB:s API för MongoDB:

[![MongoDB konsekvens modell kartläggning](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvensnivåer och kompatibilitet mellan Azure Cosmos DB API:er med API:er med öppen källkod. Se följande artiklar:

* [Tillgänglighets- och prestandaavvägningar för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [MongoDB-funktioner som stöds av Azure Cosmos DB:s API för MongoDB](mongodb-feature-support.md)
* [Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra API](cassandra-support.md)
