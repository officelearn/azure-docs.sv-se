---
title: Optimera Spark-jobb för prestanda – Azure HDInsight
description: Visa vanliga strategier för bästa prestanda för Apache Spark kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: aa5329c6321866fd26e393b581702a392f510108
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936850"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimera Apache Spark jobb i HDInsight

Lär dig hur du optimerar [Apache Spark](https://spark.apache.org/) kluster konfiguration för din specifika arbets belastning.  Den vanligaste utmaningen är minnes belastning på grund av felaktiga konfigurationer (särskilt fel storleks körningar), långvariga åtgärder och uppgifter som resulterar i kartesiska-åtgärder. Du kan påskynda jobben med lämplig cachelagring och genom att tillåta [data skevning](#optimize-joins-and-shuffles). För bästa prestanda kan du övervaka och granska långvarig körning av Spark-jobb och köra resurs krävande jobb.

I följande avsnitt beskrivs vanliga Spark-jobb optimeringar och rekommendationer.

## <a name="choose-the-data-abstraction"></a>Välj data abstraktion

Tidigare Spark-versioner använder RDD till abstrakta data, Spark 1,3 och 1,6 införde DataFrames och data uppsättningar. Tänk på följande relativa fördelar:

* **DataFrames**
    * Bästa valet i de flesta situationer.
    * Tillhandahåller optimering av frågor via katalysator.
    * Generering av hel stegs kod.
    * Direkt minnes åtkomst.
    * Låg skräp insamlings kostnad (GC).
    * Inte som utvecklare-läsvänlig som data uppsättningar, eftersom det inte finns några kompilerings kontroller eller programmering av domän objekt.
* **Data uppsättningar**
    * Bra i komplexa ETL-pipelines där prestanda påverkan är acceptabel.
    * Inte lämpligt i agg regeringar där prestanda påverkan kan vara avsevärd.
    * Tillhandahåller optimering av frågor via katalysator.
    * Utvecklare – läsvänlig genom att tillhandahålla programmering av domän objekt och kompilerings tid.
    * Lägger till omkostnader för serialisering/deserialisering.
    * Hög global kostnad.
    * Delar upp kod generation i hela fasen.
* **RDD**
    * Du behöver inte använda RDD, om du inte behöver bygga en ny anpassad RDD.
    * Ingen fråga optimering via katalysator.
    * Ingen hel stegs kod genereras.
    * Hög global kostnad.
    * Måste använda Spark 1. x äldre API: er.

## <a name="use-optimal-data-format"></a>Använd optimalt data format

Spark stöder många format, till exempel CSV, JSON, XML, Parquet, Orc och Avro. Spark kan utökas för att ge stöd för många fler format med externa data källor – mer information finns i [Apache Spark-paket](https://spark-packages.org).

Det bästa formatet för prestanda är Parquet med *Fästnings komprimering*, vilket är standard i Spark 2. x. Parquet lagrar data i kolumn format och optimeras mycket i Spark.

## <a name="select-default-storage"></a>Välj standard lagring

När du skapar ett nytt Spark-kluster kan du välja Azure Blob Storage eller Azure Data Lake Storage som kluster standard lagring. Båda alternativen ger dig fördelen med långsiktig lagring för tillfälliga kluster, så dina data tas inte bort automatiskt när du tar bort klustret. Du kan återskapa ett tillfälligt kluster och fortfarande komma åt dina data.

| Lagrings typ | Filsystem | Hastighet | Tillfälliga | Användningsfall |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:** //URL/ | **Standard** | Ja | Tillfälligt kluster |
| Azure Blob Storage (säker) | **wasbs:** //URL/ | **Standard** | Ja | Tillfälligt kluster |
| Azure Data Lake Storage gen 2| **ABFS:** //URL/ | **Tid** | Ja | Tillfälligt kluster |
| Azure Data Lake Storage Gen1| **ADL:** //URL/ | **Tid** | Ja | Tillfälligt kluster |
| Lokal HDFS | **HDFS:** //URL/ | **Snabbaste** | Nej | Interaktivt 24/7-kluster |

## <a name="use-the-cache"></a>Använd cachen

Spark tillhandahåller egna inbyggda funktioner för cachelagring som kan användas på olika sätt, till exempel `.persist()`, `.cache()`och `CACHE TABLE`. Denna inbyggda cachelagring är effektiv med små data uppsättningar samt i ETL-pipelines där du behöver cachelagra mellanliggande resultat. Spark-intern cachelagring fungerar dock för närvarande inte bra med partitionering, eftersom en cachelagrad tabell inte behåller partitionerings data. En mer generisk och tillförlitlig caching-teknik är *cachelagring av lagrings lager*.

* Native Spark-cachelagring (rekommenderas inte)
    * Passar små data uppsättningar.
    * Fungerar inte med partitionering, vilket kan ändras i framtida Spark-versioner.

* Cachelagring på lagrings nivå (rekommenderas)
    * Kan implementeras med hjälp av [Alluxio](https://www.alluxio.org/).
    * Använder minnes-och SSD-cachelagring.

* Lokal HDFS (rekommenderas)
    * `hdfs://mycluster`sökväg.
    * Använder SSD-cachelagring.
    * Cachelagrade data går förlorade när du tar bort klustret, vilket kräver en återuppbyggnad av cache.

## <a name="use-memory-efficiently"></a>Använd minne effektivt

Spark arbetar genom att placera data i minnet, så att hantering av minnes resurser är en viktig aspekt i att optimera körningen av Spark-jobb.  Det finns flera tekniker som du kan använda för att använda klustrets minne på ett effektivt sätt.

* Föredra mindre datapartitioner och konto för data storlek, typer och distribution i din partitionerings strategi.
* Överväg att använda den nyare, mer effektiva [kryo Dataserialisering](https://github.com/EsotericSoftware/kryo)i stället för standard-Java-serialisering.
* Föredra att använda garn, eftersom det separeras `spark-submit` av batch.
* Övervaka och justera konfigurations inställningar för Spark.

För din referens visas Spark-minneskortet och vissa viktiga utförar-minnes parametrar i nästa bild.

### <a name="spark-memory-considerations"></a>Överväganden vid Spark-minne

Om du använder [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)kontrollerar garn den högsta mängden minne som används av alla behållare på varje spark-nod.  Följande diagram visar de viktigaste objekten och deras relationer.

![GARN Spark minnes hantering](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Prova följande om du vill ta bort meddelanden om slut på minne:

* Läs om DAG hantering, blandade. Minska genom att dra av och på mappnings sidan, för bucketiseras-källdata, maximera enskilda blandade kanaler och minska mängden data som skickas.
* Föredra `ReduceByKey` med den fasta minnes gränsen till `GroupByKey`, som tillhandahåller agg regeringar, fönster och andra funktioner, men har den obegränsade minnes gränsen för Ann.
* Föredra `TreeReduce`, som fungerar mer i körnings-eller partitionerna, till `Reduce`, som gör allt arbete på driv rutinen.
* Utnyttja DataFrames i stället för RDD-objekt på lägre nivå.
* Skapa ComplexTypes som kapslar in åtgärder, till exempel "Top N", olika agg regeringar eller fönster åtgärder.

## <a name="optimize-data-serialization"></a>Optimera Dataserialisering

Spark-jobb distribueras, så lämplig data serialisering är viktig för bästa möjliga prestanda.  Det finns två alternativ för serialisering för Spark:

* Java-serialisering är standard.
* Kryo-serialisering är ett nyare format och kan resultera i snabbare och mer kompakt serialisering än Java.  Kryo kräver att du registrerar klasserna i ditt program och ännu inte har stöd för alla serialiserbara typer.

## <a name="use-bucketing"></a>Använd Bucket

Bucket liknar data partitionering, men varje Bucket kan innehålla en uppsättning kolumn värden i stället för bara en. Bucket fungerar bra för partitionering på stora (i miljon tals eller fler) värden, t. ex. produkt identifierare. En Bucket bestäms genom hashing av radens Bucket-nyckel. Bucked-tabeller ger unika optimeringar eftersom de lagrar metadata om hur de är i Bucket och sorterade.

Vissa avancerade Bucket-funktioner är:

* Fråga optimering baserat på Bucket meta-information.
* Optimerade agg regeringar.
* Optimerade kopplingar.

Du kan använda partitionering och Bucket på samma tid.

## <a name="optimize-joins-and-shuffles"></a>Optimera kopplingar och blanda

Om du har långsamma jobb för en koppling eller blanda är orsaken förmodligen *dataskevning*, som är asymmetry i dina jobb data. Till exempel kan ett kart jobb ta 20 sekunder, men att köra ett jobb där data är anslutna eller blandade tar timmar. Om du vill åtgärda data skevningen bör du salta hela nyckeln eller använda ett *isolerat salt* för vissa nycklar. Om du använder ett isolerat salt bör du ytterligare filtrera för att isolera din delmängd av saltade nycklar i kart kopplingar. Ett annat alternativ är att introducera en Bucket-kolumn och församlad i Bucket först.

En annan faktor som orsakar långsamma kopplingar kan vara kopplings typen. Som standard använder `SortMerge` Spark typen Join. Den här typen av anslutning lämpar sig bäst för stora data mängder, men är i övrigt kostsam eftersom det måste först sortera vänster och höger om data innan de sammanfogas.

En `Broadcast` koppling passar bäst för mindre data uppsättningar eller där en sida av kopplingen är mycket mindre än den andra sidan. Den här typen av anslutning sänder ut en sida till alla körningar och kräver därför mer minne för sändningar i allmänhet.

Du kan ändra kopplings typen i konfigurationen genom att ange `spark.sql.autoBroadcastJoinThreshold`eller så kan du ange ett JOIN-tips med DataFrame-API`dataframe.join(broadcast(df2))`: erna ().

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

Om du använder Bucket tabeller har du en tredje kopplings typ, `Merge`-kopplingen. En korrekt fördelad och försorterad data uppsättning hoppar över den dyra sorterings fasen från `SortMerge` en koppling.

Ordningen på kopplingar, särskilt i mer komplexa frågor. Börja med de mest selektiva kopplingarna. Du kan också flytta kopplingar som ökar antalet rader efter AGG regeringar när det är möjligt.

Om du vill hantera parallellitet för kartesiska-kopplingar kan du lägga till kapslade strukturer, fönster och kanske hoppa över ett eller flera steg i ditt Spark-jobb.

## <a name="customize-cluster-configuration"></a>Anpassa kluster konfiguration

Beroende på arbets belastningen i Spark-klustret kan du bestämma att en icke-standard-Spark-konfiguration skulle resultera i mer optimerat Spark-jobb.  Utför benchmark-testning med exempel arbets belastningar för att verifiera alla klusterkonfigurationer som inte är standard.

Här följer några vanliga parametrar som du kan justera:

* `--num-executors`anger lämpligt antal körningar.
* `--executor-cores`anger antalet kärnor för varje utförar. Normalt bör du ha mellanstora körningar, eftersom andra processer använder en del av det tillgängliga minnet.
* `--executor-memory`anger minnes storleken för varje utförar, som styr heap-storleken på garn. Du bör lämna lite minne för att utföra omkostnader.

### <a name="select-the-correct-executor-size"></a>Välj rätt utförar-storlek

När du bestämmer din utförar-konfiguration bör du tänka på hur du ska använda ^ skräp insamlingen (GC).

* Faktorer för att minska utförar storlek:
    1. Minska heap-storleken under 32 GB för att behålla GC-omkostnader < 10%.
    2. Minska antalet kärnor för att behålla GC-omkostnader < 10%.

* Faktorer för att öka utförar storlek:
    1. Minska kommunikations kostnader mellan körningar.
    2. Minska antalet öppna anslutningar mellan körningar (N2) i större kluster (> 100 körningar).
    3. Öka heap-storleken så att den passar för minnes intensiva uppgifter.
    4. Valfritt: Minska minnes omkostnader per utförar.
    5. Valfritt: Öka användningen och samtidigheten med oversubscribing CPU.

Som en allmän tumregel när du väljer utförar storlek:

1. Börja med 30 GB per utförar och distribuera tillgängliga maskin kärnor.
2. Öka antalet utförar-kärnor för större kluster (> 100-körningar).
3. Ändra storlek baserat på både vid utvärderings körning och på föregående faktorer, till exempel GC-overhead.

Tänk på följande när du kör samtidiga frågor:

1. Börja med 30 GB per utförar och alla dator kärnor.
2. Skapa flera parallella Spark-program med oversubscribing CPU (cirka 30% fördröjnings förbättring).
3. Distribuera frågor över parallella program.
4. Ändra storlek baserat på både vid utvärderings körning och på föregående faktorer, till exempel GC-overhead.

Övervaka dina frågeresultat för avvikande eller andra prestanda problem genom att titta på vyn tids linje, SQL graf, jobb statistik och så vidare. Ibland är ett eller flera av körningarna långsammare än de andra, och uppgifter tar mycket längre tid att köra. Detta händer ofta i större kluster (> 30 noder). I det här fallet delar du in arbetet i ett större antal aktiviteter så att Scheduler kan kompensera för långsamma aktiviteter. Du kan till exempel ha minst två gånger så många uppgifter som antalet utförar-kärnor i programmet. Du kan också aktivera spekulativ körning av uppgifter med `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optimera jobb körningen

* Cachelagra vid behov, till exempel om du använder data två gånger och sedan cachelagrar det.
* Broadcast-variabler till alla körningar. Variablerna serialiseras bara en gång, vilket resulterar i snabbare sökningar.
* Använd trådpoolen på driv rutinen, vilket leder till snabbare drift för många aktiviteter.

Övervaka dina jobb som körs regelbundet för prestanda problem. Om du behöver mer information om vissa problem kan du överväga något av följande verktyg för prestanda profilering:

* [Verktyget Intel PAL](https://github.com/intel-hadoop/PAT) övervakar processor-, lagrings-och nätverks bandbredds användning.
* [Oracle Java 8-uppdrags kontroll](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profiler Spark och utförar-kod.

Nyckeln till Spark 2. x-frågans prestanda är Tungsten-motorn, som är beroende av kodgenerering i hela fasen. I vissa fall kan generering av kod i hela fasen inaktive ras. Om du till exempel använder en icke-föränderligt typ (`string`) i agg regerings `SortAggregate` uttrycket visas i stället för `HashAggregate`. För bättre prestanda kan du till exempel prova följande och sedan återaktivera kodgenerering:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Apache Spark-jobb som körs i Azure HDInsight](apache-spark-job-debugging.md)
* [Hantera resurser för ett Apache Spark kluster i HDInsight](apache-spark-resource-manager.md)
* [Använd Apache Spark REST API för att skicka Fjärrjobb till ett Apache Spark-kluster](apache-spark-livy-rest-interface.md)
* [Justerings Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Så här finjusterar du dina Apache Spark jobb så att de fungerar](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo-serialisering](https://github.com/EsotericSoftware/kryo)
