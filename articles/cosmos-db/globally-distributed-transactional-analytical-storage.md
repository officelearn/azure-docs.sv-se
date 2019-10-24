---
title: Globalt distribuerad transaktions-och analys lagring för Azure Cosmos-behållare
description: Lär dig mer om transaktions-och analys lagring och deras konfigurations alternativ för Azure Cosmos-behållare.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: abf222b7a6d6e8fd053fa83c066d2b7850f575ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756908"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globalt distribuerad transaktions-och analys lagring för Azure Cosmos-behållare

Azure Cosmos-behållaren backas internt upp av två lagrings motorer – transaktions lagrings motor och en uppdaterbar analys lagrings motor. Båda lagrings motorerna är i logg strukturerade och skrivbara för snabbare uppdateringar. Var och en av dem kodas dock på olika sätt:

* **Transaktions lagrings motor** – den är kodad i rad orienterad form för snabba transaktions läsningar och frågor.

* **Analys lagrings motor** – den är kodad i kolumn format för snabba analytiska frågor och genomsökningar.

![Storage-motorer och Azure Cosmos DB API-mappning](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Transaktions lagrings motorn backas upp av lokala SSD, medan analys lagringen lagras på en billig SSD-lagring. I följande tabell samlas de viktiga skillnaderna mellan transaktions-och analys lagringen.


|Funktion  |Transaktions lagring  |Analytisk lagring |
|---------|---------|---------|
|Maximalt lagrings utrymme per Azure Cosmos-behållare |   Obegränsat      |    Obegränsat     |
|Maximalt lagrings utrymme per logisk partitionsnyckel   |   10 GB      |   Obegränsat      |
|Lagrings kodning  |   Rad orienterad, med ett internt format.   |   Kolumn-orienterade, med Apache Parquet-format. |
|Lagrings plats |   Replikerad lagring som backas upp av lokala/intra-Cluster-SSD. |  Replikerad lagring som backas upp av ett billigt fjärr-eller SSD-kluster.       |
|Hållbarhet  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|API: er som har åtkomst till data  |   SQL, MongoDB, Cassandra, Gremlin, tables och Etcd.       | Apache Spark         |
|Kvarhållning (Time-to-Live eller TTL)   |  Princip driven, konfigurerad i Azure Cosmos-behållaren med hjälp av egenskapen `DefaultTimeToLive`.       |   Princip driven, konfigurerad i Azure Cosmos-behållaren med hjälp av egenskapen `ColumnStoreTimeToLive`.      |
|Pris per GB    |   $0,25/GB      |  $0,02/GB       |
|Pris för lagrings transaktioner    | Det tillhandahållna data flödet debiteras med $0,008 per 100 RU/s med fakturering per timme.        |  Förbruknings data flöde debiteras med $0,05 för 10 000 Skriv transaktioner och $0,004 för 10 000 Läs transaktioner.       |

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

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>Ögonblicks bilder på begäran och tid rese analys

Du kan ta ögonblicks bilder av dina data som lagras i analys lagringen av dina Azure Cosmos-behållare när som helst genom att anropa kommandot `CreateSnapshot (name, timestamp)` på behållaren. Ögonblicks bilder kallas "bok märken" i historiken för de uppdateringar som har utförts på din behållare.

![Ögonblicks bilder på begäran och tid rese analys](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

När du skapar ögonblicks bilden kan du förutom namnet ange tidsstämpeln som definierar statusen för din behållare i historiken över uppdateringar. Du kan sedan läsa in ögonblicks bild data till Spark och utföra frågorna.

För närvarande kan du bara ta ögonblicks bilder på begäran när som helst på behållaren. möjligheten att automatiskt ta ögonblicks bilder baserat på ett schema eller en anpassad princip stöds inte ännu.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>Konfigurera och skikta data mellan transaktions-och analys lagring oberoende av varandra

Beroende på ditt scenario kan du självständigt aktivera eller inaktivera var och en av de två lagrings motorerna. Följande är konfigurationerna för varje scenario:

|Scenario |Inställning av transaktions lagring  |Inställning för analys lagring |
|---------|---------|---------|
|Köra analytiska arbets belastningar exklusivt (med oändlig kvarhållning) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|Köra transaktions arbets belastningar exklusivt (med oändlig kvarhållning)  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|Köra både transaktions-och analys arbets belastningar (med oändlig kvarhållning)   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|Köra både transaktions-och analys arbets belastningar (med olika kvarhållningsintervall, även kallade lagrings nivåer)  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **Konfigurera behållaren exklusivt för analytiska arbets belastningar (med oändlig kvarhållning)**

   Du kan konfigurera din Azure Cosmos-behållare exklusivt för analytiska arbets belastningar. Den här konfigurationen har en fördel där du inte behöver betala för transaktions lagringen. Om målet endast är att använda behållaren för analytiska arbets belastningar kan du inaktivera transaktions lagringen genom att ange `DefaultTimeToLive` till 0 i behållaren Cosmos och du kan aktivera analytisk lagring med oändlig kvarhållning genom att ange `ColumnStoreTimeToLive` till-1.

   ![Analytiska arbets belastningar med oändlig kvarhållning](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **Konfigurera behållaren exklusivt för transaktions arbets belastningar (med oändlig kvarhållning)**

   Du kan konfigurera din Azure Cosmos-behållare exklusivt för transaktions arbets belastningar. Du kan inaktivera analys lagringen genom att ange `ColumnStoreTimeToLive` till 0 på behållaren och du kan aktivera analytisk lagring med oändlig kvarhållning genom att ange `DefaultTimeToLive` till-1.

   ![Transaktions arbets belastningar med oändlig kvarhållning](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **Konfigurera behållaren för både transaktions-och analys arbets belastningar (med oändlig kvarhållning)**

   Du kan konfigurera din Azure Cosmos-behållare för både transaktions-och analys arbets belastningar med fullständig prestanda isolering mellan dem. Du kan aktivera analytisk lagring genom att ange `ColumnStoreTimeToLive` till-1, och aktivera transaktions lagring med oändlig kvarhållning genom att ange `DefaultTimeToLive ` till-1.

   ![Transaktionella och analytiska arbets belastningar med oändlig kvarhållning](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **Konfigurera behållaren för både transaktions-och analys arbets belastningar med lagrings nivåer**

   Du kan konfigurera din Azure Cosmos-behållare för både transaktions-och analys arbets belastningar med fullständig prestanda isolering mellan dem med olika kvarhållningsintervall. Azure Cosmos DB tvingar fram att analys lagringen alltid behålls under en längre tid än transaktions lagringen.

   Du kan aktivera transaktions lagring med oändlig kvarhållning genom att ange `DefaultTimeToLive` till < värde 1 > och aktivera analytisk lagring genom att ställa in `ColumnStoreTimeToLive` < värde 2 >. Azure Cosmos DB tvingar att < värde 2 > alltid är större än < värde 1 >.

   ![Transaktionella och analytiska arbets belastningar med lagrings nivåer](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>Nästa steg

* [Tid till Live i Azure Cosmos DB](time-to-live.md)
