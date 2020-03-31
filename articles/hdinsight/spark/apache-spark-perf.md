---
title: Optimera Spark-jobb för prestanda – Azure HDInsight
description: Visa vanliga strategier för bästa prestanda för Apache Spark-kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 3d8f4a28961be7e0ece517e00026d9711d8f67e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198879"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimera Apache Spark-jobb i HDInsight

Lär dig hur du optimerar Apache Spark-klusterkonfigurationen för just din arbetsbelastning. [Apache Spark](https://spark.apache.org/)  Den vanligaste utmaningen är minnestryck, på grund av felaktiga konfigurationer (särskilt fel storlek executors), långvariga åtgärder och uppgifter som resulterar i kartesiska åtgärder. Du kan snabba upp jobb med lämplig cachelagring och genom att tillåta [datasnedställning](#optimize-joins-and-shuffles). För bästa prestanda kan du övervaka och granska tidskrävande och resurskrävande Spark-jobbkörningar. Information om hur du kommer igång med Apache Spark på HDInsight finns i [Skapa Apache Spark-kluster med Azure Portal](apache-spark-jupyter-spark-sql-use-portal.md).

I följande avsnitt beskrivs vanliga Spark-jobboptimeringar och rekommendationer.

## <a name="choose-the-data-abstraction"></a>Välj dataabstraktion

Tidigare Spark-versioner använder RDD:er för att abstrakta data, Spark 1.3 och 1.6 introducerade DataFrames respektive DataSets. Tänk på följande relativa meriter:

* **DataFrames**
    * Bästa valet i de flesta situationer.
    * Ger frågeoptimering via Catalyst.
    * Kodgenerering i hela steget.
    * Direkt minnesåtkomst.
    * Låg sophämtning (GC) overhead.
    * Inte lika utvecklarvänlig som DataSets, eftersom det inte finns några kompileringstidskontroller eller domänobjektprogrammering.
* **Datamängder**
    * Bra i komplexa ETL-rörledningar där prestandapåverkan är acceptabel.
    * Inte bra i aggregeringar där prestandapåverkan kan vara betydande.
    * Ger frågeoptimering via Catalyst.
    * Utvecklarvänlig genom att tillhandahålla domänobjektprogrammering och kompileringstidskontroller.
    * Lägger till serialisering/deserialisering omkostnader.
    * Hög GC overhead.
    * Bryter hela steg kodgenerering.
* **RDDs**
    * Du behöver inte använda RDD:er, såvida du inte behöver skapa en ny anpassad RDD.
    * Ingen frågeoptimering via Catalyst.
    * Ingen kodgenerering i hela fasen.
    * Hög GC overhead.
    * Måste använda Spark 1.x äldre API:er.

## <a name="use-optimal-data-format"></a>Använd optimalt dataformat

Spark stöder många format, till exempel csv, json, xml, parkett, orc och avro. Spark kan utökas för att stödja många fler format med externa datakällor - mer information finns i [Apache Spark-paket](https://spark-packages.org).

Det bästa formatet för prestanda är parkett med *snärtig komprimering*, som är standard i Spark 2.x. Parkett lagrar data i columnar-format och är mycket optimerat i Spark.

## <a name="select-default-storage"></a>Välj standardlagring

När du skapar ett nytt Spark-kluster kan du välja Azure Blob Storage eller Azure Data Lake Storage som klustrets standardlagring. Båda alternativen ger dig fördelen med långsiktig lagring för tillfälliga kluster, så att dina data inte tas bort automatiskt när du tar bort klustret. Du kan återskapa ett tillfälligt kluster och fortfarande komma åt dina data.

| Butikstyp | Filsystem | Hastighet | Övergående | Användningsfall |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Ja | Övergående kluster |
| Azure Blob Storage (säker) | **wasbs:**//url/ | **Standard** | Ja | Övergående kluster |
| Azure Data Lake Storage Gen 2| **abfs:**//url/ | **Snabbare** | Ja | Övergående kluster |
| Azure Data Lake Storage Gen 1| **adl:**//url/ | **Snabbare** | Ja | Övergående kluster |
| Lokala HDFS | **hdfs:**//url/ | **Snabbaste** | Inga | Interaktivt 24/7-kluster |

En fullständig beskrivning av de lagringsalternativ som är tillgängliga för HDInsight-kluster finns i [Jämför lagringsalternativ för användning med Azure HDInsight-kluster](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Använda cacheminnet

Spark tillhandahåller sina egna inbyggda cachemekanismer, som kan `.persist()`användas `.cache()`med `CACHE TABLE`olika metoder som , och . Den här inbyggda cachelagringen är effektiv med små datauppsättningar samt i ETL-pipelines där du behöver cachelagra mellanliggande resultat. Spark native caching fungerar dock inte bra med partitionering, eftersom en cachelagrad tabell inte behåller partitioneringsdata. En mer generisk och tillförlitlig cachelagringsteknik är *cachelagring av lagringslager.*

* Inbyggd sparkcachening (rekommenderas inte)
    * Bra för små datamängder.
    * Fungerar inte med partitionering, vilket kan ändras i framtida Spark-versioner.

* Cachelagringsnivå (rekommenderas)
    * Kan implementeras på HDInsight med hjälp av [IO-cachefunktionen.](apache-spark-improve-performance-iocache.md)
    * Använder cachelagring i minnet och SSD.

* Lokala HDFS (rekommenderas)
    * `hdfs://mycluster`Sökvägen.
    * Använder SSD-cachelagring.
    * Cachelagrade data går förlorade när du tar bort klustret, vilket kräver en cacherenad.

## <a name="use-memory-efficiently"></a>Använda minnet effektivt

Spark fungerar genom att placera data i minnet, så att hantera minnesresurser är en viktig aspekt för att optimera körningen av Spark-jobb.  Det finns flera tekniker som du kan använda för att använda klustrets minne effektivt.

* Föredrar mindre datapartitioner och ta hänsyn till datastorlek, typer och distribution i din partitioneringsstrategi.
* Tänk på den nyare, effektivare [Kryo-dataseriering](https://github.com/EsotericSoftware/kryo), snarare än standard Java serialisering.
* Föredrar att använda YARN, `spark-submit` eftersom det separerar efter parti.
* Övervaka och justera konfigurationsinställningarna för Spark.

Som referens visas Spark-minnesstrukturen och några viktiga parametrar för körminne i nästa bild.

### <a name="spark-memory-considerations"></a>Spark minne överväganden

Om du använder [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)styr YARN den maximala summan minne som används av alla behållare på varje Spark-nod.  I följande diagram visas nyckelobjekten och deras relationer.

![HANTERING AV YARN Spark-minne](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Om du vill ta itu med meddelanden om på minne provar du:

* Granska DAG Management Shuffles. Minska med omduktion på kartsidan, förpartitionsdata (eller bucketize), maximera enskilda blandningar och minska mängden data som skickas.
* Föredrar `ReduceByKey` med dess fasta `GroupByKey`minnesgräns till , som ger aggregeringar, fönster och andra funktioner, men det har ann unbounded minnesgräns.
* Föredrar `TreeReduce`, vilket gör mer arbete på executors eller partitioner, till `Reduce`, som gör allt arbete på drivrutinen.
* Utnyttja DataFrames i stället för rdd-objekt på lägre nivå.
* Skapa ComplexTypes som kapslar in åtgärder, till exempel "Topp N", olika aggregeringar eller fönsteråtgärder.

Ytterligare felsökningssteg finns i [Undantag för OutOfMemoryError för Apache Spark i Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="optimize-data-serialization"></a>Optimera dataseriering

Spark-jobb distribueras, så lämplig dataseriering är viktig för bästa prestanda.  Det finns två serialiseringsalternativ för Spark:

* Java serialisering är standard.
* Kryo serialisering är ett nyare format och kan resultera i snabbare och mer kompakt serialisering än Java.  Kryo kräver att du registrerar klasserna i ditt program, och det stöder ännu inte alla Serializable typer.

## <a name="use-bucketing"></a>Använda skopor

Bucketing liknar datapartitionering, men varje bucket kan innehålla en uppsättning kolumnvärden i stället för bara en. Bucketing fungerar bra för partitionering på stora (i miljoner eller fler) antal värden, till exempel produktidentifierare. En hink bestäms genom att du hashar öftonnyckeln på raden. Bucketed-tabeller erbjuder unika optimeringar eftersom de lagrar metadata om hur de var bucketed och sorterade.

Några avancerade bucketing funktioner är:

* Frågeoptimering baserat på bucketing-metainformation.
* Optimerade aggregeringar.
* Optimerade kopplingar.

Du kan använda partitionering och bucketing samtidigt.

## <a name="optimize-joins-and-shuffles"></a>Optimera kopplingar och blandningar

Om du har långsamma jobb på en koppling eller shuffle är orsaken förmodligen *datasnedställning*, vilket är asymmetri i jobbdata. Ett kartjobb kan till exempel ta 20 sekunder, men det tar timmar att köra ett jobb där data är sammanfogade eller blandade. Om du vill åtgärda datasnedveringen bör du salta hela nyckeln eller använda ett *isolerat salt* för endast en delmängd av nycklar. Om du använder ett isolerat salt bör du ytterligare filtrera för att isolera delmängden av saltade nycklar i kartkopplingar. Ett annat alternativ är att införa en bucket kolumn och föraggregerade i hinkar först.

En annan faktor som orsakar långsamma kopplingar kan vara kopplingstypen. Som standard använder `SortMerge` Spark kopplingstypen. Den här typen av koppling passar bäst för stora datauppsättningar, men är annars beräkningsmässigt dyr eftersom den först måste sortera vänster och höger sida av data innan de slås samman.

En `Broadcast` koppling passar bäst för mindre datauppsättningar, eller där den ena sidan av kopplingen är mycket mindre än den andra sidan. Den här typen av koppling sänder en sida till alla utförare och kräver därför mer minne för sändningar i allmänhet.

Du kan ändra kopplingstypen `spark.sql.autoBroadcastJoinThreshold`i konfigurationen genom att ange , eller`dataframe.join(broadcast(df2))`så kan du ange en kopplingstips med hjälp av DataFrame API:er ( ).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Om du använder bucketed-tabeller har du en tredje `Merge` kopplingstyp, kopplingen. En korrekt förpartitionerad och försorterad datauppsättning hoppar över den `SortMerge` dyra sorteringsfasen från en koppling.

Ordningen på kopplingarna, särskilt i mer komplexa frågor. Börja med de mest selektiva kopplingarna. Flytta också kopplingar som ökar antalet rader efter aggregeringar när det är möjligt.

Om du vill hantera parallellism för kartesiska kopplingar kan du lägga till kapslade strukturer, fönster och kanske hoppa över ett eller flera steg i spark-jobbet.

## <a name="customize-cluster-configuration"></a>Anpassa klusterkonfiguration

Beroende på din Spark-klusterarbetsbelastning kan du bestämma att en spark-konfiguration som inte är standard skulle resultera i mer optimerad Spark-jobbkörning.  Utför benchmark-testning med exempelarbetsbelastningar för att validera klusterkonfigurationer som inte är standard.

Här är några vanliga parametrar som du kan justera:

* `--num-executors`anger lämpligt antal utförare.
* `--executor-cores`anger antalet kärnor för varje utförare. Vanligtvis bör du ha medelstora executors, eftersom andra processer förbrukar en del av det tillgängliga minnet.
* `--executor-memory`anger minnesstorleken för varje executor, som styr heapstorleken på YARN. Du bör lämna lite minne för körning overhead.

### <a name="select-the-correct-executor-size"></a>Välj rätt körstorlek

När du bestämmer din executor-konfiguration bör du tänka på GC-kostnaderna (Java garbage collection).

* Faktorer för att minska utförare storlek:
    1. Minska heap storlek under 32 GB för att hålla GC overhead < 10%.
    2. Minska antalet kärnor för att hålla GC overhead < 10%.

* Faktorer för att öka utförarens storlek:
    1. Minska kommunikationen mellan utförare.
    2. Minska antalet öppna anslutningar mellan executors (N2) på större kluster (>100 utförare).
    3. Öka heap-storleken så att den passar för minnesintensiva uppgifter.
    4. Valfritt: Minska omkostnader per körning.
    5. Valfritt: Öka utnyttjandet och samtidigheten genom att översubscribing CPU.

Som en allmän tumregel när du väljer utförarens storlek:

1. Börja med 30 GB per utförare och distribuera tillgängliga maskinkärnor.
2. Öka antalet körkärnor för större kluster (> 100 utförare).
3. Ändra storlek baserat både på utvärderingskörningar och på föregående faktorer, till exempel GC overhead.

När du kör samtidiga frågor bör du tänka på följande:

1. Börja med 30 GB per executor och alla maskinkärnor.
2. Skapa flera parallella Spark-program genom att översubscribing CPU (cirka 30% latens förbättring).
3. Distribuera frågor över parallella program.
4. Ändra storlek baserat både på utvärderingskörningar och på föregående faktorer, till exempel GC overhead.

Mer information om hur du använder Ambari för att konfigurera executors finns i [Apache Spark-inställningar - Spark-körare](apache-spark-settings.md#configuring-spark-executors).

Övervaka frågeprestanda för extremvärden eller andra prestandaproblem genom att titta på tidslinjevyn, SQL-diagrammet, jobbstatistiken och så vidare. Information om felsökning av Spark-jobb med YARN och Spark History-servern finns i [Felsökningsservern För Apache Spark som körs på Azure HDInsight](apache-spark-job-debugging.md). Tips om hur du använder YARN Timeline Server finns i [Access Apache Hadoop YARN-programloggar](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Ibland är en eller några av utförarna långsammare än de andra, och det tar mycket längre tid att utföra uppgifter. Detta händer ofta på större kluster (> 30 noder). I det här fallet delar du upp arbetet i ett större antal aktiviteter så att schemaläggaren kan kompensera för långsamma aktiviteter. Du har till exempel minst dubbelt så många aktiviteter som antalet executor-kärnor i programmet. Du kan också aktivera spekulativ körning av uppgifter med `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optimera jobbkörning

* Cache efter behov, till exempel om du använder data två gånger och sedan cachelagrar den.
* Broadcast-variabler till alla utförare. Variablerna serialiseras bara en gång, vilket resulterar i snabbare uppslag.
* Använd trådpoolen på drivrutinen, vilket resulterar i snabbare drift för många aktiviteter.

Övervaka dina jobb som körs regelbundet för prestandaproblem. Om du behöver mer insikt i vissa problem bör du överväga något av följande verktyg för prestandaprofilering:

* [Intel PAL Tool](https://github.com/intel-hadoop/PAT) övervakar användningen av CPU, lagring och nätverksbandbredd.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profiler Spark och executor kod.

Nyckeln till Spark 2.x frågeprestanda är volframmotorn, som är beroende av kodgenerering i hela steget. I vissa fall kan kodgenerering i hela fasen inaktiveras. Om du till exempel använder en icke-föränderlig typ (`string`) `SortAggregate` i `HashAggregate`aggregeringsuttrycket visas i stället för . Om du till exempel vill ha bättre prestanda kan du prova följande och sedan återaktivera kodgenerering:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Apache Spark-jobb som körs i Azure HDInsight](apache-spark-job-debugging.md)
* [Hantera resurser för ett Apache Spark-kluster på HDInsight](apache-spark-resource-manager.md)
* [Använd Apache Spark REST API för att skicka fjärrjobb till ett Apache Spark-kluster](apache-spark-livy-rest-interface.md)
* [Trimma Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Hur man faktiskt tune din Apache Spark jobb så att de fungerar](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo serialisering](https://github.com/EsotericSoftware/kryo)
