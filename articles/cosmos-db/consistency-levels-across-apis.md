---
title: Konsekvensnivåer och API:er för Azure Cosmos DB
description: 'Förstå konsekvens nivå mappning mellan olika API: er i Azure Cosmos DB och Apache Cassandra, MongoDB'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/6/2020
ms.reviewer: sngun
ms.openlocfilehash: bb8a413f2e2a3aa4a8facd533d822312bb61fa0e
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613568"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsekvensnivåer och API:er för Azure Cosmos DB

Azure Cosmos DB har inbyggt stöd för API-kompatibla API: er för populära databaser. Dessa omfattar MongoDB, Apache Cassandra, Gremlin och Azure Table Storage. De här databaserna erbjuder inte exakt definierade konsekvens modeller eller garanti garantier för konsekvens nivå. De tillhandahåller vanligt vis endast en delmängd av de fem konsekvens modellerna som erbjuds av Azure Cosmos DB.

När du använder SQL API, Gremlin API och Tabell-API, används standard konsekvens nivån som kon figurer ATS på Azure Cosmos-kontot. 

När du använder API för Cassandra eller Azure Cosmos DB s API för MongoDB får program en fullständig uppsättning konsekvens nivåer som erbjuds av Apache Cassandra och MongoDB, med ännu bättre konsekvens och hållbarhets säkerhet. Det här dokumentet visar motsvarande Azure Cosmos DB konsekvens nivåer för konsekvens nivåer av Apache Cassandra och MongoDB.

> [!NOTE]
> Standard konsekvens modellen för Azure Cosmos DB är session. Session är en klientbaserad konsekvens modell som inte stöds internt av antingen Cassandra eller MongoDB. Mer information om vilken konsekvens modell som ska väljas finns [i avsnittet om konsekvens nivåer i Azure Cosmos DB](consistency-levels.md)

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Mappning mellan Apache Cassandra och Azure Cosmos DB konsekvens nivåer

Till skillnad från Azure Cosmos DB ger Apache Cassandra inte inbyggt exakt definierade konsekvens garantier.  I stället ger Apache Cassandra en Skriv konsekvens nivå och en Läs konsekvens nivå för att möjliggöra hög tillgänglighet, konsekvens och latens fördröjningar. När du använder Azure Cosmos DB API för Cassandra: 

* Den Skriv konsekvens nivån för Apache Cassandra mappas till standard konsekvens nivån som kon figurer ATS på ditt Azure Cosmos-konto. Konsekvens för en Skriv åtgärd (CL) kan inte ändras per begäran.

* Azure Cosmos DB mappar dynamiskt den Läs konsekvens nivå som anges av Cassandra-klient driv rutinen till en av Azure Cosmos DB konsekvens nivåer som kon figurer ATS dynamiskt på en läsbegäran. 

I följande tabell visas hur de interna Cassandra-konsekvens nivåerna mappas till Azure Cosmos DBs konsekvens nivåer när du använder API för Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra konsekvens modell mappning" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mappning mellan MongoDB-och Azure Cosmos DB konsekvens nivåer

Till skillnad från Azure Cosmos DB ger det inbyggda MongoDB inte exakt definierade konsekvens garantier. I stället tillåter interna MongoDB att användare konfigurerar följande konsekvens garantier: en Skriv angelägenhet, en läsning och isMaster-direktivet – för att dirigera Läs åtgärder till antingen primära eller sekundära repliker för att uppnå önskad konsekvens nivå.

När du använder Azure Cosmos DBs API för MongoDB, behandlar MongoDB-drivrutinen din Skriv region som den primära repliken och alla andra regioner är läsa replik. Du kan välja vilken region som är kopplad till ditt Azure Cosmos-konto som en primär replik. 

När du använder Azure Cosmos DBs API för MongoDB:

* Skriv skyddet mappas till standard konsekvens nivån som kon figurer ATS på ditt Azure Cosmos-konto.

* Azure Cosmos DB mappar dynamiskt det läsnings problem som anges av MongoDB-klient driv rutinen till en av de Azure Cosmos DB konsekvens nivåer som har kon figurer ATS dynamiskt på en Read-begäran.  

* Du kan kommentera en speciell region som är kopplad till ditt Azure Cosmos-konto som "primär" genom att göra regionen till den första skrivbara regionen. 

I följande tabell illustreras hur de inbyggda Skriv-och Läs problemen i MongoDB mappas till konsekvens nivåerna i Azure Cosmos när du använder Azure Cosmos DB s API för MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Cassandra konsekvens modell mappning" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvens nivåer och kompatibilitet mellan Azure Cosmos DB-API: er med API: er med öppen källkod. Se följande artiklar:

* [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](consistency-levels-tradeoffs.md)
* [MongoDB-funktioner som stöds av Azure Cosmos DBs API för MongoDB](mongodb-feature-support.md)
* [Apache Cassandra-funktioner som stöds av Azure Cosmos DB API för Cassandra](cassandra-support.md)
