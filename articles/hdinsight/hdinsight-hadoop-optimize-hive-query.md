---
title: Optimera Hive-frågor i Azure HDInsight | Microsoft Docs
description: Lär dig hur du optimerar dina Hive-frågor för Hadoop i HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: e844b4f1e9898347da6af589dbfe41bf2ad3ab69
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Optimera Hive-frågor i Azure HDInsight

Hadoop-kluster är som standard inte optimerats för prestanda. Den här artikeln beskriver vissa vanligaste Hive prestanda optimering-metoder som du kan tillämpa på dina frågor.

## <a name="scale-out-worker-nodes"></a>Skala ut arbetsnoder

Öka antalet arbetarnoder i ett kluster kan utnyttja mer mappers och förminskningsapparater köras parallellt. Det finns två sätt som du kan öka skala ut i HDInsight:

* Du kan ange antalet arbetarnoder med Azure-portalen, Azure PowerShell eller plattformsoberoende kommandoradsgränssnittet när etablera.  Mer information finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Följande skärmbild visar worker nodkonfiguration på Azure portal:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* Vid körning, kan du även skala ut ett kluster utan att återskapa en:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Läs mer om de olika virtuella datorer som stöds av HDInsight [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Aktivera Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) är en alternativ Körningsmotor för motorn MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez går snabbare eftersom:

* **Köra dirigeras acykliska diagram (DAG) som ett enda jobb i motorn MapReduce**. Gruppen för Databastillgänglighet kräver varje uppsättning mappers ska följas av en uppsättning förminskningsapparater. Detta medför flera MapReduce-jobb kan vara roterade för varje Hive-fråga. Tez har inte sådan begränsning och kan bearbeta komplexa DAG som ett jobb, vilket minimerar jobbet startade kostnader.
* **Undviker onödig skrivningar**. På grund av flera jobb som skapas för samma Hive-fråga i motorn MapReduce, skrivs utdata från varje jobb till HDFS för mellanliggande data. Eftersom Tez minimerar antalet jobb för varje Hive-fråga är det kunna undvika onödiga skrivåtgärder.
* **Minimerar uppstart fördröjningar**. Tez är bättre att minimera uppstart fördröjning genom att minska antalet mappers som behövs för att starta och även förbättra optimering i hela.
* **Återanvänder behållare**. När möjliga Tez kan återanvända behållare för att säkerställa att fördröjning på grund av att starta behållare minskas.
* **Kontinuerlig optimeringstekniker**. Traditionellt gjordes optimering under kompilering fasen. Men mer information om den angivna informationen är tillgänglig som möjliggör bättre optimering under körningen. Tez använder kontinuerlig optimeringstekniker som tillåter att optimera plan i runtime-fasen.

Mer information om dessa koncept finns [Apache TEZ](http://hortonworks.com/hadoop/tez/).

Du kan göra en Hive-fråga Tez aktiverad med frågan med inställningen nedan:

    set hive.execution.engine=tez;

Linux-baserade HDInsight-kluster har Tez aktiverad som standard.


## <a name="hive-partitioning"></a>Hive partitionering

I/o-åtgärden är större flaskhals för att köra Hive-frågor. Prestanda kan förbättras om du kan minska mängden data som ska läsas. Sök igenom hela Hive-tabeller som standard Hive-frågor. Det här är bra för frågor som tabellsökningar. Men för frågor som behöver bara söka igenom en liten mängd data (till exempel frågor med filtrering), detta skapar onödiga omkostnader. Hive partitionering gör Hive-frågor att få åtkomst till endast nödvändiga mängden data i Hive-tabeller.

Hive partitionering implementeras genom att ordna rådata i nya kataloger med varje partition har en egen katalog - där den definieras av användaren. Följande diagram illustrerar partitionering en Hive-tabell som kolumnen *år*. En ny katalog skapas för varje år.

![Partitionering][image-hdi-optimize-hive-partitioning_1]

Vissa partitionering att tänka på:

* **Gör inte under partition** -partitionering på kolumner med bara några värden kan orsaka några partitioner. Till exempel skapar partitionering på kön bara två partitioner som ska skapas (han och hondjur) kan därför endast minska fördröjningen med högst hälften.
* **Gör inte över partition** , på andra extreme, skapa en partition på en kolumn med ett unikt värde (t.ex, användar-ID) medför flera partitioner. Över partitionen gör att mycket belastningen på klustret namenode eftersom den har att hantera ett stort antal kataloger.
* **Undvika data skeva** -Välj din partitionsnyckel klokt så att alla partitioner har även storlek. Ett exempel partitionering på *tillstånd* kan orsaka antalet poster under California ska nästan 30 x som Vermont på grund av skillnader i populationen.

Så här skapar du en partitionstabell den *partitioneras av* sats:

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Du kan antingen skapa partitionering statisk eller dynamisk partitionering när partitionerade tabellen har skapats.

* **Statisk partitionering** innebär att du har redan shardade data i rätt kataloger och du kan be Hive partitioner manuellt utifrån katalogplatsen. Följande kodavsnitt är ett exempel.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Dynamisk partitionering** innebär det att du vill Hive för att skapa partitioner automatiskt åt dig. Eftersom det redan har skapat tabellen partitionering från mellanlagringstabellen är vi behöver infoga data i partitionerade tabellen:
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Mer information finns i [partitionerade tabeller](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Använd formatet ORCFile
Hive stöder olika filformat. Exempel:

* **Text**: Detta är standardfilformatet och fungerar med de flesta scenarier
* **Avro**: fungerar bra för heterogena miljöer
* **ORC/parkettgolv**: bäst prestanda

ORC (optimerad raden kolumner)-formatet är ett mycket effektivt sätt att lagra data med Hive. ORC har jämfört med andra format, följande fördelar:

* stöd för komplexa typer inklusive DateTime och komplicerade och halvstrukturerade typer
* upp till 70% komprimering
* indexerar var 10 000 rader, som tillåter hoppar över rader
* en betydande minskning av körning körning

Om du vill aktivera ORC format, skapar du först en tabell med satsen *lagras som ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Bredvid, infoga data till tabellen ORC från mellanlagringstabellen. Exempel:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
            L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Du kan läsa mer i formatet ORC [här](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Vectorization kan Hive att bearbeta en batch med 1024 rader tillsammans i stället för bearbetning av en rad i taget. Det innebär att enkla åtgärder utförs snabbare eftersom mindre intern kod måste köras.

Om du vill aktivera prefixet vectorization Hive-fråga med följande inställning:

    set hive.vectorized.execution.enabled = true;

Mer information finns i [Vectorized Frågekörningen](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Andra optimeringsmetoder för
Det finns flera optimeringsmetoder som du kan tänka på, till exempel:

* **Hive bucketing:** en teknik som gör att klustret eller segmentera stora anger data för att optimera prestanda för frågor.
* **Anslut optimering:** optimering av registreringsdata Frågekörningen planering att förbättra effektiviteten för kopplingar och minska behovet av att användaren tips. Mer information finns i [ansluta optimering](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Öka förminskningsapparater**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig flera vanliga Hive-fråga optimeringsmetoder. Mer information finns i följande artiklar:

* [Använda Apache Hive i HDInsight](hadoop/hdinsight-use-hive.md)
* [Analysera svarta fördröjning data med hjälp av Hive i HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analysera Twitter-data med Hive i HDInsight](hdinsight-analyze-twitter-data.md)
* [Analysera sensordata med Hive-fråga konsolen på Hadoop i HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Använda Hive med HDInsight för att analysera loggar från webbplatser](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
