---
title: Globalt distribuerad transaktions- och analyslagring(i privat förhandsversion) för Azure Cosmos-behållare
description: Lär dig mer om transaktions- och analyslagring och deras konfigurationsalternativ för Azure Cosmos-behållare.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623381"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globalt distribuerad transaktions- och analyslagring för Azure Cosmos-behållare

Azure Cosmos-behållaren backas upp internt av två lagringsmotorer – transaktionslagringsmotor och en uppdateringsbar analytisk lagringsmotor (i privat förhandsversion). Båda lagringsmotorerna är loggstrukturerade och skrivoptimerade för snabbare uppdateringar. Var och en av dem är dock kodad på olika sätt:

* **Transaktionslagringsmotor** – Den kodas i radorienterat format för snabba transaktionsläsningar och frågor.

* **Analytisk lagringsmotor** - Den är kodad i columnar-format för snabba analytiska frågor och skanningar.

![Lagringsmotorer och Azure Cosmos DB API-mappning](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Transaktionslagringsmotorn backas upp av lokala SSD-enheter, medan den analytiska lagringen lagras på en billig SSD-lagring utanför klustret. I följande tabell fångas de anmärkningsvärda skillnaderna mellan transaktionslagringen och den analytiska lagringen.


|Funktion  |Transaktionslagring  |Analytisk lagring |
|---------|---------|---------|
|Maximal lagring per Azure Cosmos-behållare |   Unlimited      |    Unlimited     |
|Maximal lagring per logisk partitionsnyckel   |   20 GB      |   Unlimited      |
|Lagringskodning  |   Radorienterad med ett internt format.   |   Kolumnorienterad, med Apache Parquet format. |
|Lagringslokal |   Replikerad lagring som backas upp av lokala/interna SSD-enheter. |  Replikerad lagring som backas upp av billiga fjärr-/off-cluster-SSD:er.       |
|Hållbarhet  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|API:er som kommer åt data  |   SQL, MongoDB, Cassandra, Gremlin, Tabeller och etcd.       | Apache Spark         |
|Kvarhållning (Time-to-live eller TTL)   |  Principdriven, konfigurerad på Azure Cosmos-behållaren med hjälp av egenskapen. `DefaultTimeToLive`       |   Principdriven, konfigurerad på Azure Cosmos-behållaren med hjälp av egenskapen. `ColumnStoreTimeToLive`      |
|Pris per GB    |   Se [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Se [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Pris för lagringstransaktioner    |  Se [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Se [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Fördelar med transaktions- och analyslagring

### <a name="no-etl-operations"></a>Inga ETL-operationer

Traditionella analytiska rörledningar är komplexa med flera steg vardera som kräver ETL-åtgärder (Extract-Transform-Load) till och från beräknings- och lagringsnivåerna. Det resulterar i komplexa databehandlingsarkitekturer. Vilket innebär höga kostnader för flera stadier av lagring och beräkning, och hög latens på grund av massiva volymer av data som överförs mellan olika stadier av lagring och beräkning.  

Det finns ingen overhead för att utföra ETL-åtgärder med Azure Cosmos DB. Varje Azure Cosmos-behållare backas upp av både transaktions- och analyslagringsmotorer, och dataöverföringen mellan transaktions- och analyslagringsmotorn sker i databasmotorn och utan nätverkshopp. Den resulterande latensen och kostnaden är betydligt lägre jämfört med traditionella analyslösningar. Och du får ett enda globalt distribuerat lagringssystem för både transaktions- och analysarbetsbelastningar.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Lagra flera versioner, uppdatera och fråga om analyslagring

Den analytiska lagringen är helt uppdateringsbar och innehåller den fullständiga versionshistoriken för alla transaktionsuppdateringar som inträffade på Azure Cosmos-behållaren.

Alla uppdateringar som görs i transaktionslagringen är garanterat synliga för den analytiska lagringen inom 30 sekunder. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globalt distribuerad analytisk lagring av flera befälhavare

Om ditt Azure Cosmos-konto är begränsat till en enda region begränsas även data som lagras (i transaktions- och analyslagring) i behållarna till en enda region. Å andra sidan, om Azure Cosmos-kontot distribueras globalt, distribueras även data som lagras i behållarna globalt.

För Azure Cosmos-konton som konfigurerats med flera skrivregioner utförs skrivningar till behållaren (till både transaktions- och analyslagring) alltid i den lokala regionen och därför är de snabba.

För azure Cosmos-konton med en eller flera regioner, oavsett om enstaka skrivregioner (enstaka huvudnamn) eller flera skrivregioner (kallas även multi-master), utförs både transaktions- och analytiska läsningar/frågor lokalt i den angivna regionen.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Effektivitetsisolering mellan transaktions- och analysarbetsbelastningar

I en viss region arbetar transaktionsarbetsbelastningarna mot behållarens transaktions-/radlagring. Å andra sidan fungerar den analytiska arbetsbelastningen mot behållarens analytiska/kolumnlagring. De två lagringsmotorerna fungerar oberoende av dem och ger strikt prestandaisolering mellan arbetsbelastningarna.

Transaktionsarbetsbelastningarna förbrukar det etablerade dataflödet (RU: er). Till skillnad från transaktionsarbetsbelastningar baseras analysarbetsbelastningsdataflödet på den faktiska förbrukningen. De analytiska arbetsbelastningarna förbrukar resurser på begäran.

## <a name="next-steps"></a>Nästa steg

* [Dags att bo i Azure Cosmos DB](time-to-live.md)
