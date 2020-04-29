---
title: Globalt distribuerad transaktion och analys (i privat förhands granskning) lagring för Azure Cosmos-behållare
description: Lär dig mer om transaktions-och analys lagring och deras konfigurations alternativ för Azure Cosmos-behållare.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77623381"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globalt distribuerad transaktions-och analys lagring för Azure Cosmos-behållare

Azure Cosmos-behållaren backas internt upp av två lagrings motorer – transaktions lagrings motor och en uppdaterbar analys lagrings motor (i privat förhands granskning). Båda lagrings motorerna är i logg strukturerade och skrivbara för snabbare uppdateringar. Var och en av dem kodas dock på olika sätt:

* **Transaktions lagrings motor** – den är kodad i rad orienterad form för snabba transaktions läsningar och frågor.

* **Analys lagrings motor** – den är kodad i kolumn format för snabba analytiska frågor och genomsökningar.

![Storage-motorer och Azure Cosmos DB API-mappning](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Transaktions lagrings motorn backas upp av lokala SSD, medan analys lagringen lagras på en billig SSD-lagring. I följande tabell samlas de viktiga skillnaderna mellan transaktions-och analys lagringen.


|Funktion  |Transaktions lagring  |Analytisk lagring |
|---------|---------|---------|
|Maximalt lagrings utrymme per Azure Cosmos-behållare |   Obegränsat      |    Obegränsat     |
|Maximalt lagrings utrymme per logisk partitionsnyckel   |   20 GB      |   Obegränsat      |
|Lagrings kodning  |   Rad orienterad, med ett internt format.   |   Kolumn-orienterade, med Apache Parquet-format. |
|Lagrings plats |   Replikerad lagring som backas upp av lokala/intra-Cluster-SSD. |  Replikerad lagring som backas upp av ett billigt fjärr-eller SSD-kluster.       |
|Hållbarhet  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|API: er som har åtkomst till data  |   SQL, MongoDB, Cassandra, Gremlin, tables och etcd.       | Apache Spark         |
|Kvarhållning (Time-to-Live eller TTL)   |  Princip driven, konfigurerad i Azure Cosmos-behållaren med hjälp `DefaultTimeToLive` av egenskapen.       |   Princip driven, konfigurerad i Azure Cosmos-behållaren med hjälp `ColumnStoreTimeToLive` av egenskapen.      |
|Pris per GB    |   Se [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Se [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Pris för lagrings transaktioner    |  Se [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Se [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Fördelar med transaktions-och analys lagring

### <a name="no-etl-operations"></a>Inga ETL-åtgärder

Traditionella analytiska pipeliner är komplexa med flera steg som kräver att ETL-åtgärder (Extract-Transform-Load) används för att och från beräknings-och lagrings nivåerna. Det resulterar i komplexa data bearbetnings arkitekturer. Det innebär höga kostnader för flera faser av lagring och beräkning, och hög latens på grund av stora mängder data som överförs mellan olika stadier av lagring och beräkning.  

Det finns inga kostnader för att utföra ETL-åtgärder med Azure Cosmos DB. Varje Azure Cosmos-behållare backas upp av både transaktions-och analysbaserade lagrings motorer och data överföringen mellan transaktions-och analys lagrings motorn görs i databas motorn och utan några nätverks hopp. Den resulterande svars tiden och kostnaden är betydligt lägre jämfört med traditionella analys lösningar. Och du får ett enda globalt distribuerat lagrings system för både transaktions-och analys arbets belastningar.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Lagra flera versioner, uppdatera och fråga analys lagringen

Analys lagringen är helt uppdaterings bar och innehåller den fullständiga versions historiken för alla transaktionella uppdateringar som ingick i Azure Cosmos-behållaren.

Alla uppdateringar som görs i transaktions lagringen garanteras vara synliga för analys lagringen inom 30 sekunder. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globalt distribuerad analys lagring med flera huvud servrar

Om ditt Azure Cosmos-konto är begränsat till en enda region, är de data som lagras (i transaktions-och analys lagring) i behållarna också begränsade till en enda region. Å andra sidan distribueras även de data som lagras i behållarna globalt distribuerade om Azure Cosmos-kontot är globalt distribuerat.

För Azure Cosmos-konton som kon figurer ATS med flera Skriv regioner utförs skrivningar till behållaren (till både transaktions-och analys lagring) alltid i den lokala regionen och därför är de snabba.

För både Azure Cosmos-konton med en eller flera regioner, oavsett om det finns en enskild skrivnings region (Single Master) eller flera Skriv regioner (även kallat multi-master), utförs både transaktionella och analytiska läsningar/frågor lokalt i den aktuella regionen.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Prestanda isolering mellan transaktions-och analys arbets belastningar

I en specifik region fungerar transaktions arbets belastningarna mot din behållares transaktions-och rad lagring. Å andra sidan körs de analytiska arbets belastningarna mot behållarens analys-/kolumn lagring. De två lagrings motorerna körs oberoende och ger strikt prestanda isolering mellan arbets belastningarna.

Transaktions arbets belastningarna förbrukar det etablerade data flödet (ru: er). Till skillnad från transaktions arbets belastningar baseras data flödet för analytiska arbets belastningar på den faktiska förbrukningen. De analytiska arbets belastningarna förbrukar resurser på begäran.

## <a name="next-steps"></a>Nästa steg

* [Tid till Live i Azure Cosmos DB](time-to-live.md)
