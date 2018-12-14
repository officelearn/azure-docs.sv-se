---
title: Optimera Hive-frågor i Azure HDInsight
description: Den här artikeln beskriver hur du optimerar dina Apache Hive-frågor för Hadoop i HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 57e23c55342ee397ecb8590dd6da639ba766f351
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385439"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optimera Apache Hive-frågor i Azure HDInsight

I Azure HDInsight finns flera klustertyper och tekniker som kan köra Apache Hive-frågor. När du skapar ditt HDInsight-kluster väljer du lämplig klustertypen för att optimera prestanda för dina arbetsbelastningsbehov. 

Till exempel välja **interaktiv fråga** klustertyp att optimera för ad hoc, interaktiva frågor. Välj Apache **Hadoop** klustertyp att optimera för Hive-frågor som används som en batchprocess. **Spark** och **HBase** klustertyper kan också köra Hive-frågor. Mer information om hur du kör Hive-frågor på olika typer av HDInsight-kluster finns i [vad är Apache Hive och HiveQL på Azure HDInsight?](hadoop/hdinsight-use-hive.md).

HDInsight-kluster av typ av Hadoop-kluster är inte optimerade för prestanda som standard. Den här artikeln beskriver några av de vanligaste Hive prestanda optimering-metoder som du kan tillämpa på dina frågor.

## <a name="scale-out-worker-nodes"></a>Skala ut arbetsnoder

Öka antalet arbetarnoder i ett HDInsight-kluster kan arbetet att utnyttja fler Mappningskomponenter och reducerare som ska köras parallellt. Det finns två sätt att öka skala ut i HDInsight:

* På gång när du skapar ett kluster, kan du ange antalet arbetsnoder som använder Azure-portalen, Azure PowerShell eller kommandoradsgränssnittet.  Mer information finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Följande skärmbild visar arbetaren nodkonfiguration på Azure portal:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
    
* När du har skapat, kan du också redigera antalet arbetarnoder att skala ut ytterligare ett kluster utan att återskapa en:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Läs mer om att skala HDInsight [skala HDInsight-kluster](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Använda Apache Tez i stället för Map Reduce

[Apache Tez](https://hortonworks.com/hadoop/tez/) är en alternativ Körningsmotor till MapReduce-motorn. Linux-baserade HDInsight-kluster har Tez aktiverat som standard.

![tez_1][image-hdi-optimize-hive-tez_1]

Tez är snabbare eftersom:

* **Köra riktad Acyklisk graf (DAG) som ett enskilt jobb i MapReduce-motorn**. Gruppen för Databastillgänglighet kräver varje uppsättning Mappningskomponenter ska följas av en uppsättning reducerare. Detta leder till flera MapReduce-jobb att vara kunde för varje Hive-fråga. Tez saknar sådan begränsning och kan bearbeta komplexa DAG som ett jobb, vilket minimerar jobbet Start overhead.
* **På så sätt undviker onödiga skrivningar**. Flera jobb används för att bearbeta samma Hive-frågan i MapReduce-motorn. Utdata för varje MapReduce-jobb skrivs till HDFS för mellanliggande data. Eftersom Tez minimerar antalet jobb för varje Hive-fråga, är det att undvika onödiga skrivningar.
* **Minimerar Start fördröjningar**. Tez är bättre att minimera fördröjning Start genom att minska antalet Mappningskomponenter som behövs för att starta och även förbättra optimering i hela.
* **Återanvänder behållare**. När möjligt Tez kan återanvända behållare för att säkerställa att fördröjning på grund av startar behållare minskas.
* **Kontinuerlig optimeringstekniker**. Traditionellt gjordes optimering under fasen för kompilering. Men mer information om indata är tillgängliga som gör det möjligt att den optimeras under körning. Tez använder kontinuerlig optimeringstekniker som gör det att optimera planen i runtime-fas.

Mer information om de här koncepten finns i [Apache TEZ](https://hortonworks.com/hadoop/tez/).

Du kan göra eventuella Hive-fråga Tez aktiverat genom frågan med följande set-kommando:

   ```hive
   set hive.execution.engine=tez;
   ```

## <a name="hive-partitioning"></a>Hive partitionering

I/o-åtgärder är större flaskhals för att köra Hive-frågor. Prestanda kan förbättras om kan minska mängden data som behöver läsas. Sök igenom hela Hive-tabeller som standard Hive-frågor. Men för frågor som behöver bara skanna en liten mängd data (till exempel frågor med filtrering), det här beteendet skapar onödiga overhead. Hive partitionering kan Hive-frågor att få åtkomst till endast nödvändiga mängden data i Hive-tabeller.

Hive partitionering implementeras genom att ordna rådata i nya kataloger. Varje partition har en egen filkatalog. Partitionering har definierats av användaren. Följande diagram illustrerar partitionering en Hive-tabell som kolumnen *år*. En ny katalog skapas för varje år.

![Hive partitionering][image-hdi-optimize-hive-partitioning_1]

Vissa partitionering att tänka på:

* **Gör inte under partition** -partitionering för kolumner med bara några värden kan orsaka några partitioner. Till exempel skapar partitionering på kön bara två partitioner som ska skapas (kvinnliga och manliga), vilket endast minska fördröjningen med högst hälften.
* **Gör inte över partition** , på andra yttersta, skapa en partition på en kolumn med ett unikt värde (till exempel användar-ID) gör att flera partitioner. Över partitionen gör att mycket stress på klustret namenode eftersom den har att hantera ett stort antal kataloger.
* **Undvika datasnedställning** -Välj din partitionsnyckel motiverar så att alla partitioner har även storlek. Till exempel partitionering på *tillstånd* kolumn kan förskjuta fördelning av data. Eftersom myndigheter i Kalifornien har en population nästan 30 x att av Vermont, partitionsstorleken är potentiellt förvrängd och prestanda kan variera oerhört mycket.

Du kan skapa en partitionstabell med den *partitioneras av* sats:

   ```hive
   CREATE TABLE lineitem_part
       (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING, 
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
   STORED AS TEXTFILE;
   ```
   
Du kan antingen skapa partitionering statisk eller dynamisk partitionering när partitionerade tabellen har skapats.

* **Statisk partitionering** innebär att du har redan shardade data i vissa kataloger. Med statiska partitioner, att lägga till Hive-partitioner manuellt utifrån katalogplatsen. Följande kodavsnitt är ett exempel.
  
   ```hive
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
   SELECT * FROM lineitem 
   WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
   
   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
   LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```
   
* **Dynamisk partitionering** innebär det att du vill Hive för att skapa partitioner automatiskt åt dig. Eftersom du redan har skapat tabellen partitionering från mellanlagringstabellen är allt du behöver göra infoga data i partitionerade tabellen:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE, 
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```
   
Mer information finns i [partitionerade tabeller](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Använd formatet ORCFile
Hive stöder olika filformat. Exempel:

* **Text**: standardfilformat och fungerar med de flesta scenarier.
* **Avro**: fungerar bra för heterogena miljöer.
* **ORC/Parquet**: bäst lämpade för prestanda.

(Optimerad rad kolumner) ORC-format är ett mycket effektivt sätt att lagra Hive-data. ORC har jämfört med andra format, följande fördelar:

* stöd för komplexa typer inklusive DateTime och komplexa och delvis strukturerade typer.
* upp till 70% komprimering.
* indexerar varje 10 000 rader, som gör att hoppa över rader.
* en betydande nedgång i körningsmiljön för körning.

Om du vill aktivera ORC-format, skapar du först en tabell med satsen *lagras som ORC*:

   ```hive
   CREATE TABLE lineitem_orc_part
       (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   STORED AS ORC;
   ```
   
Nu ska infoga du data i ORC-tabellen från mellanlagringstabellen. Exempel:

   ```hive
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
   ```
   
Du kan läsa mer på ORC-format i den [Apache Hive Language manual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Vectorization kan Hive att bearbeta en batch med 1024 rader tillsammans i stället för bearbetning av en rad i taget. Det innebär att enkla åtgärder utförs snabbare eftersom mindre intern kod ska köras.

Om du vill aktivera prefixet vectorization Hive-frågan med följande inställning:

   ```hive
    set hive.vectorized.execution.enabled = true;
   ```

Mer information finns i [Vectorized Frågekörningen](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Andra optimeringsmetoder för
Det finns flera optimeringsmetoder som du kan tänka på, till exempel:

* **Hive-bucket:** en teknik som gör att klustret eller segmentera stora uppsättningar data att optimera frågeprestanda.
* **Ansluta till optimering:** optimering av Hives Frågekörningen planerar att förbättra effektiviteten för kopplingar och minska behovet av att användaren tips. Mer information finns i [delta optimering](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Öka Reducerare**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig flera vanliga optimeringsmetoder för Hive-fråga. Mer information finns i följande artiklar:

* [Använda Apache Hive i HDInsight](hadoop/hdinsight-use-hive.md)
* [Analysera flygförseningsdata med hjälp av Apache Hive i HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analysera Twitter-data med Apache Hive i HDInsight](hdinsight-analyze-twitter-data.md)
* [Analysera sensordata med hjälp av konsolen Apache Hive-fråga på Apache Hadoop i HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Använda Apache Hive med HDInsight för att analysera loggar från webbplatser](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
