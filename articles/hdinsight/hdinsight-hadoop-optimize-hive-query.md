---
title: Optimera Hive-frågor i Azure HDInsight
description: I den här artikeln beskrivs hur du optimerar dina Apache Hive-frågor för Hadoop i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 4955df718dcc8f169232052979ccf4a636c3be80
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390301"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optimera Apache Hive-frågor i Azure HDInsight

I Azure HDInsight finns det flera klustertyper och tekniker som kan köra Apache Hive-frågor. Välj lämplig klustertyp för att optimera prestanda för dina arbetsbelastningsbehov.

Välj till exempel **klustertyp för** `ad hoc`interaktiva frågor för att optimera för interaktiva frågor. Välj Apache **Hadoop-klustertyp** för att optimera för Hive-frågor som används som en batchprocess. **Spark-** och **HBase-klustertyper** kan också köra Hive-frågor. Mer information om hur du kör Hive-frågor på olika HDInsight-klustertyper finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hadoop/hdinsight-use-hive.md).

HDInsight-kluster av Hadoop-klustertyp är inte optimerade för prestanda som standard. I den här artikeln beskrivs några av de vanligaste Hive-prestandaoptimeringsmetoderna som du kan använda på dina frågor.

## <a name="scale-out-worker-nodes"></a>Skala ut arbetsnoder

Om du ökar antalet arbetsnoder i ett HDInsight-kluster kan arbetet använda fler mappers och reducerare som ska köras parallellt. Det finns två sätt att öka skala ut i HDInsight:

* När du skapar ett kluster kan du ange antalet arbetsnoder med Azure-portalen, Azure PowerShell eller kommandoradsgränssnittet.  Mer information finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Följande skärmbild visar konfigurationen av arbetsnoden på Azure-portalen:
  
    ![Noder för azure-portalsklusterstorlek](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* När du har skapat det kan du också redigera antalet arbetsnoder för att skala ut ett kluster ytterligare utan att återskapa ett:

    ![Klusterstorlek för Azure Portal-skala](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

Mer information om hur du skalar HDInsight finns i [Skala HDInsight-kluster](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Använd Apache Tez istället för Att minska kartan

[Apache Tez](https://tez.apache.org/) är en alternativ exekveringsmotor till MapReduce-motorn. Linux-baserade HDInsight-kluster har Tez aktiverat som standard.

![Översiktsdiagram för HDInsight Apache Tez](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez är snabbare eftersom:

* **Kör riktad acyklisk graf (DAG) som ett enda jobb i MapReduce-motorn**. DAG kräver att varje uppsättning mappers följs av en uppsättning reducerare. Det här kravet gör att flera MapReduce-jobb avknoppades för varje Hive-fråga. Tez har inte en sådan begränsning och kan bearbeta komplexa DAG som ett jobb minimera jobb start overhead.
* **Undviker onödiga skrivningar**. Flera jobb används för att bearbeta samma Hive-fråga i MapReduce-motorn. Utdata för varje MapReduce-jobb skrivs till HDFS för mellanliggande data. Eftersom Tez minimerar antalet jobb för varje Hive-fråga kan den undvika onödiga skrivningar.
* **Minimerar uppstartsfördröjningar**. Tez är bättre på att minimera startfördröjning genom att minska antalet mappers det behöver för att starta och även förbättra optimering hela.
* **Återanvänder behållare**. När det är möjligt kommer Tez att återanvända behållare för att säkerställa att latensen från att starta behållare minskas.
* **Kontinuerlig optimeringstekniker**. Traditionellt gjordes optimering under kompileringsfasen. Men mer information om indata finns tillgänglig som möjliggör bättre optimering under körning. Tez använder kontinuerlig optimeringstekniker som gör det möjligt att optimera planen längre in i körningsfasen.

Mer information om dessa begrepp finns i [Apache TEZ](https://tez.apache.org/).

Du kan göra valfri Hive-fråga Tez aktiverad genom att prefixera frågan med följande uppsättningskommando:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Uppdelning av Hive

I/O-åtgärder är den största flaskhalsen för prestanda för att köra Hive-frågor. Prestanda kan förbättras om mängden data som behöver läsas kan minskas. Som standard genomsöker Hive-frågor hela Hive-tabeller. Men för frågor som bara behöver skanna en liten mängd data (till exempel frågor med filtrering) skapar detta beteende onödiga omkostnader. Hive-partitionering gör att Hive-frågor kan komma åt endast den nödvändiga mängden data i Hive-tabeller.

Hive-partitionering implementeras genom att omorganisera rådata till nya kataloger. Varje partition har en egen filkatalog. Partitioneringen definieras av användaren. Följande diagram illustrerar partitionering av en Hive-tabell efter kolumnen *År*. En ny katalog skapas för varje år.

![HDInsight Apache Hive-partitionering](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Några partitionering överväganden:

* **Inte under partition** - Partitionering på kolumner med endast ett fåtal värden kan orsaka få partitioner. Partitionering på kön skapar till exempel bara två partitioner som ska skapas (man och kvinna), så minska svarstiden med högst hälften.
* **Don't over partition** - På den andra ytterligheten orsakar skapandet av en partition i en kolumn med ett unikt värde (till exempel userid) flera partitioner. Över partition orsakar mycket stress på klusternamnnoden eftersom den måste hantera det stora antalet kataloger.
* **Undvik data skeva** - Välj din partitioneringsnyckel klokt så att alla partitioner är jämna storlek. Partitionering i *kolumnen Tillstånd* kan till exempel skeva distributionen av data. Eftersom delstaten Kalifornien har en befolkning nästan 30x som Vermont, är partitionen storlek potentiellt skev och prestanda kan variera enormt.

Om du vill skapa en partitionstabell använder du *partitionerad* efter-satsen:

```sql
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

När den partitionerade tabellen har skapats kan du antingen skapa statisk partitionering eller dynamisk partitionering.

* **Statisk partitionering** innebär att du redan har fragmenterade data i lämpliga kataloger. Med statiska partitioner lägger du till Hive-partitioner manuellt baserat på katalogplatsen. Följande kodavsnitt är ett exempel.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Dynamisk partitionering** innebär att du vill att Hive ska skapa partitioner automatiskt åt dig. Eftersom du redan har skapat partitioneringstabellen från mellanlagringstabellen behöver du bara infoga data i den partitionerade tabellen:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Mer information finns i [Partitionerade tabeller](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Använda ORCFile-formatet

Hive stöder olika filformat. Ett exempel:

* **Text**: standardfilformatet och fungerar med de flesta scenarier.
* **Avro**: fungerar bra för scenarier för interoperabilitet.
* **ORC/Parkquet**: bäst lämpad för prestanda.

ORC-format (Optimized Row Columnar) är ett mycket effektivt sätt att lagra Hive-data. Jämfört med andra format har ORC följande fördelar:

* stöd för komplexa typer, inklusive DateTime och komplexa och halvstrukturerade typer.
* upp till 70% komprimering.
* indexerar var 10 000 rader, vilket gör att du kan hoppa över rader.
* en betydande minskning av körningen av körningen.

Om du vill aktivera ORC-format skapar du först en tabell med satsen *Lagrad som ORC:*

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Därefter infogar du data i ORC-tabellen från mellanlagringstabellen. Ett exempel:

```sql
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

Du kan läsa mer om ORC-formatet i [Apache Hive Language manual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vektorisering

Vectorization tillåter Hive att bearbeta en batch med 1024 rader tillsammans i stället för att bearbeta en rad i taget. Det innebär att enkla åtgärder utförs snabbare eftersom mindre intern kod måste köras.

Så här aktiverar du vektoriseringsprefix din Hive-fråga med följande inställning:

```hive
set hive.vectorized.execution.enabled = true;
```

Mer information finns i [Vektoriserad frågekörning](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Andra optimeringsmetoder

Det finns fler optimeringsmetoder som du kan tänka på, till exempel:

* **Hive-bucketing:** en teknik som gör det möjligt att gruppera eller segmentera stora uppsättningar data för att optimera frågeprestanda.
* **Gå med optimering:** optimering av Hive fråga körning planering för att förbättra effektiviteten i kopplingar och minska behovet av användare tips. Mer information finns i [Gå med i optimering](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Öka reducerarna**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig flera vanliga Hive frågeoptimeringsmetoder. Mer information finns i följande artiklar:

* [Använd Apache Hive i HDInsight](hadoop/hdinsight-use-hive.md)
* [Analysera flygfördröjningsdata med hjälp av interaktiv fråga i HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analysera Twitter-data med Apache Hive i HDInsight](hdinsight-analyze-twitter-data-linux.md)
