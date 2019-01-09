---
title: Optimera prestanda – Azure HDInsight Spark-jobb
description: Visar vanliga strategier för den bästa prestandan av Spark-kluster.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: d1eeedfd91dfe1d4a174a3cbed2c0db826a8d5ab
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117868"
---
# <a name="optimize-apache-spark-jobs"></a>Optimera Apache Spark-jobb

Lär dig hur du optimerar [Apache Spark](https://spark.apache.org/) klusterkonfigurationen för en viss arbetsbelastning.  De vanligaste utmaningen är minnesbelastning på grund av felaktiga konfigurationer (särskilt fel storlek executors), långvariga åtgärder och uppgifter som resulterar i kartesiska åtgärder. Du kan påskynda jobb med lämpliga cachelagring och genom att [datasnedställning](#optimize-joins-and-shuffles). För bästa prestanda, övervaka och granska tidskrävande och resursintensiva Spark jobbkörningar.

I följande avsnitt beskrivs vanliga optimeringar för Spark-jobb och rekommendationer.

## <a name="choose-the-data-abstraction"></a>Välj data abstraction

Tidigare versioner av Spark använder rdd-datauppsättningar till abstrakta data, Spark 1.3 och 1.6 introducerades dataramar och datauppsättningar, respektive. Överväg följande eller nackdelar:

* **Dataramar**
    * Bästa val i de flesta situationer.
    * Innehåller Frågeoptimeringen via Catalyst.
    * Hela steg kodgenerering.
    * Direkt minnesåtkomst.
    * Liten skräpinsamling (GC) overhead.
    * Inte så utvecklarvänliga som datauppsättningar, eftersom det finns inga kompileringstid kontroller eller domän objektet programmering.
* **Datauppsättningar**
    * Bra i komplexa ETL-pipelines som där prestandapåverkan som är godtagbart.
    * Inte bra i aggregeringar där prestandapåverkan kan vara betydande.
    * Innehåller Frågeoptimeringen via Catalyst.
    * Utvecklarvänliga genom att tillhandahålla objektet programmeringsspråk och kompileringstid domänkontrollen.
    * Lägger till serialisering/deserialisering kostnader.
    * Hög GC-kostnader.
    * Delar upp hela steg kodgenerering.
* **Rdd-datauppsättningar**
    * Du behöver inte använda rdd-datauppsättningar, såvida du inte behöver skapa en ny anpassad RDD.
    * Inga Frågeoptimeringen via Catalyst.
    * Inga hela steg kodgenerering.
    * Hög GC-kostnader.
    * Använda Spark 1.x äldre API: er.

## <a name="use-optimal-data-format"></a>Använda optimala dataformat

Spark har stöd för många format, till exempel csv, json, xml, parquet, orc och avro. Spark kan utökas för att hantera många fler format med externa datakällor - mer information finns i [Apache Spark-paket](https://spark-packages.org).

Det bästa formatet för prestanda är parquet med *snappy komprimering*, vilket är standard i Spark 2.x. Parquet lagrar data i kolumnformat och är optimerade i Spark.

## <a name="select-default-storage"></a>Välj standardlagring

När du skapar ett nytt Spark-kluster har möjlighet att välja Azure Blob Storage eller Azure Data Lake Storage som standard klusterlagringen. Båda alternativen ger dig fördelen med långsiktig lagring för tillfälliga kluster, så att dina data inte automatiskt tas bort när du tar bort ditt kluster. Du kan återskapa ett tillfälligt kluster och fortfarande komma åt dina data.

| Store-typ | Filsystem | Hastighet | Tillfälligt | Användningsfall |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Ja | Tillfälliga kluster |
| Azure Data Lake Storage | **ADL:**//url/ | **Snabbare** | Ja | Tillfälliga kluster |
| Lokala HDFS | **hdfs:**//url/ | **Snabbaste** | Nej | Interaktiv 24/7-kluster |

## <a name="use-the-cache"></a>Använda cache

Spark tillhandahåller sin egen interna cachelagring mekanismer som kan användas med olika metoder som `.persist()`, `.cache()`, och `CACHE TABLE`. Den här inbyggda cachelagring är effektivt med små datauppsättningar samt som ETL-pipelines som där du behöver cache mellanliggande resultat. Dock fungerar Spark interna cachelagring för närvarande inte bra med partitionering, eftersom en cachelagrad tabell behåller partitionering data. En mer allmän och tillförlitlig cachelagring metod *storage layer cachelagring*.

* Interna Spark cachelagring (rekommenderas inte)
    * Bra för små datauppsättningar.
    * Fungerar inte med partitionering, som kan ändras i framtida versioner av Spark.

* Lagring på cachelagring (rekommenderas)
    * Kan implementeras med hjälp av [Alluxio](https://www.alluxio.org/).
    * Använder i minnet och cachelagring i SSD.

* Lokala HDFS (rekommenderas)
    * `hdfs://mycluster` sökvägen.
    * Använder SSD cachelagring.
    * Cachelagrade data går förlorade när du tar bort klustret, kräver återskapning av en cache.

## <a name="use-memory-efficiently"></a>Använda minne effektivt

Spark fungerar genom att placera data i minnet, så hanterar Minnesresurserna är en viktig aspekt av hur du optimerar körningen av Spark-jobb.  Det finns flera metoder som du kan använda för att använda ditt kluster minne effektivt.

* Föredrar mindre data-partitionerna och ta hänsyn till datastorlek, typer och distribution i din strategi för partitionering.
* Överväg att den nyare effektivare [Kryo dataserialisering](https://github.com/EsotericSoftware/kryo), i stället för standard Java-serialisering.
* Föredrar att använda YARN, som delar den upp `spark-submit` av batch.
* Övervaka och finjustera Spark-konfigurationsinställningar.

För referens visas Spark minne strukturen och vissa viktiga executor minne parametrar i nästa bild.

### <a name="spark-memory-considerations"></a>Krav på Spark-minne

Om du använder [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), och sedan YARN styr högsta summan av minne som används av alla behållare på varje nod i Spark.  Följande diagram visar viktiga objekt och deras relationer.

![YARN Spark minneshantering](./media/apache-spark-perf/yarn-spark-memory.png)

Prova följande för att åtgärda ”slut på minne”-meddelanden:

* Granska DAG Management Shuffles. Minska genom kartan sida reducting, partitionera före (eller bucketisera) källdata, maximera enda shuffles och minska mängden data som skickas.
* Föredrar `ReduceByKey` med minnesgränsen fast till `GroupByKey`, som innehåller aggregeringar, fönsterhantering och andra funktioner, men den har ann obundna minnesgräns.
* Föredrar `TreeReduce`, vilket gör mer arbete på utförare eller partitioner, till `Reduce`, vilket gör allt arbete på drivrutinen.
* Utnyttja dataramar i stället för på lägre nivå RDD-objekt.
* Skapa ComplexTypes som kapslar in åtgärder, till exempel ”Top N”, olika aggregeringar eller fönsterhantering åtgärder.

## <a name="optimize-data-serialization"></a>Optimera dataserialisering

Spark-jobb distribueras så lämpliga dataserialisering är viktigt för bästa prestanda.  Det finns två alternativ för serialisering för Spark:

* Java-serialisering är standardinställningen.
* Kryo serialisering är ett nyare format och kan resultera i snabbare och mer komprimera serialisering än Java.  Kryo kräver att du registrerar klasserna i ditt program och den stöder ännu inte alla serialiserbara typer.

## <a name="use-bucketing"></a>Använda bucket

Bucket liknar Datapartitionering, men varje bucket kan innehålla en uppsättning kolumnvärdena i stället för bara en. Bucket fungerar bra för partitionering på (i miljontal eller mer) stort antal värden, till exempel produkt-ID: n. En bucket bestäms av hash-bucket-nyckeln för raden. Bucketgrupperade tabeller innehåller unika optimeringar eftersom de lagrar metadata om hur de bucketas och sorteras.

Vissa avancerade bucket funktioner är:

* Frågeoptimeringen baserat på bucket metainformation.
* Optimerad aggregeringar.
* Optimerad kopplingar.

Du kan använda partitionering och bucket på samma gång.

## <a name="optimize-joins-and-shuffles"></a>Optimera kopplingar och shuffles

Om du har långsam jobb på en koppling eller blandad beror det förmodligen *datasnedställning*, vilket är asymmetrisk i ditt jobb. Till exempel en karta jobbet kan ta 20 sekunder, men köra ett jobb där data är ansluten eller blandad tar timmar.   Om du vill åtgärda datasnedställning, bör du salt hela nyckeln eller använda en *isolerade salt* för endast en delmängd av nycklar.  Om du använder en isolerad salt, bör du ytterligare filtrera för att isolera din underuppsättning med saltat nycklar i kartan kopplingar. Ett annat alternativ är att införa en bucket-kolumn och sammanställa förväg på buckets först.

En annan faktor som orsakar långsam kopplingar kan vara join-typen. Spark använder som standard den `SortMerge` anslutningstyp. Den här typen av koppling lämpar sig bäst för stora mängder data, men är annars beräkningsmässigt dyra eftersom den måste först sortera till vänster och höger data innan du sammanfogar dem.

En `Broadcast` join passar bäst för mindre datamängder, eller om en sida i kopplingen är mycket mindre än den andra sidan. Den här typen av koppling sänder en sida till alla executors och kräver mer minne för sändningar i allmänhet.

Du kan ändra kopplingstyp i konfigurationen genom att ange `spark.sql.autoBroadcastJoinThreshold`, eller du kan ange en join-tips med DataFrame APIs (`dataframe.join(broadcast(df2))`).

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

Om du använder bucketgrupperade tabeller så att du har en tredje kopplingstyp, den `Merge` koppling. En korrekt före partitionerade och förväg sorterade datauppsättning hoppar över fasen dyra sortera från en `SortMerge` koppling.

Ordningen på kopplingar är viktig, särskilt i mer komplexa frågor. Börja med de mest selektiv kopplingarna. Dessutom flytta kopplingar som ökar antalet rader efter aggregeringar när det är möjligt.

Om du vill hantera parallellitet, särskilt när det gäller kartesiska kopplingar, du kan lägga till kapslade strukturer fönsterhantering, och kanske hoppa över en eller flera steg i Spark-jobb.

## <a name="customize-cluster-configuration"></a>Anpassa klusterkonfigurationen

Beroende på arbetsbelastningens Spark-kluster kan du bestämma att en icke-standard Spark konfiguration skulle resultera i mer optimerade körningen av Spark-jobb.  Utföra benchmark testning med exempelarbetsbelastningar att verifiera alla klusterkonfigurationer som inte är standard.

Här följer några vanliga parametrar som du kan justera:

* `--num-executors` Anger hur många executors.
* `--executor-cores` Anger antalet kärnor för varje executor. Normalt bör du ha middle-sized executors andra processer konsumera några av det tillgängliga minnet.
* `--executor-memory` Anger minnesstorleken för varje executor som styr heap-storlek på YARN. Du bör lämna vissa minne för körning kostnader.

### <a name="select-the-correct-executor-size"></a>Välj rätt executor storlek

Tänk Java skräpinsamling samling (GC) kostnader när du bestämmer executor konfigurationen.

* Faktorer att minska executor storlek:
    1. Minska stackstorlek nedan 32 GB att hålla GC administration < 10%.
    2. Minska antalet kärnor för att hålla GC administration < 10%.

* Faktorer att öka storleken på executor:
    1. Minska kommunikation overhead mellan executors.
    2. Minska antalet öppna anslutningar mellan executors (N2) på större kluster (> 100 executors).
    3. Öka heap-storlek kan utföra vissa uppgifter.
    4. Valfritt: Minska per executor minnesanvändning.
    5. Valfritt: Öka användningen och samtidighet genom överteckning av CPU.

Som en allmän tumregel när du väljer executor storlek:
    
1. Börja med 30 GB per executor och distribuera dator tillgänglighet kärnor.
2. Öka antalet kärnor som executor för större kluster (> 100 executors).
3. Öka eller minska baseras både på testkörningar och på de föregående faktorerna som GC kostnader.

När du kör samtidiga frågor, Tänk på följande:

1. Börja med 30 GB per executor och alla datorkärnor.
2. Skapa flera parallella Spark-program med överteckning av CPU (cirka 30% svarstid förbättring).
3. Distribuera frågor över parallella program.
4. Öka eller minska baseras både på testkörningar och på de föregående faktorerna som GC kostnader.

Övervaka frågeprestandan för avvikare eller andra prestandaproblem, genom att titta på tidslinjevyn, SQL-diagram, Jobbstatistik och så vidare. En eller några av executors är ibland långsammare än de andra och aktiviteter ta mycket längre tid att köra. Detta sker ofta på större kluster (> 30 noder). Dela i så fall upp arbetet i ett större antal uppgifter så scheduler kan kompensera för långsam uppgifter. Till exempel ha minst dubbelt så många uppgifter som antalet executor kärnor i programmet. Du kan också aktivera spekulativ körning av uppgifter med `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optimera jobbkörning

* Cachelagra vid behov, till exempel om du använder data två gånger och sedan cachelagra den.
* Skicka variabler till alla executors. Variablerna serialiseras bara en gång, vilket ger snabbare sökningar.
* Använd trådpoolen på drivrutinen vilket resulterar i snabbare åtgärden för många uppgifter.

Övervaka ditt jobb som körs för prestandaproblem med jämna mellanrum. Om du behöver mer information om vissa problem med, bör du något av följande prestanda profilering verktyg:

* [Intel PAL verktyget](https://github.com/intel-hadoop/PAT) övervakar CPU-, lagrings- och bandbreddsanvändningen i nätverket.
* [Oracle Java 8 Verksamhetskontroll](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profiler Spark och executor kod.

Nyckeln till Spark 2.x frågeprestanda är volfram-motorn, vilket beror på hela steg kodgenerering. I vissa fall kan inaktiveras hela steg kodgenerering. Exempel: Om du använder en icke avstängningsbara typ (`string`) i uttrycket aggregering `SortAggregate` visas i stället för `HashAggregate`. Till exempel för bättre prestanda, provar du följande och sedan återaktivera kodgenerering:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Apache Spark-jobb som körs på Azure HDInsight](apache-spark-job-debugging.md)
* [Hantera resurser för ett Apache Spark-kluster i HDInsight](apache-spark-resource-manager.md)
* [Använda Apache Spark REST API för att skicka fjärrstyrda jobb till ett Apache Spark-kluster](apache-spark-livy-rest-interface.md)
* [Justering av Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Hur du ställer in faktiskt din Apache Spark-jobb, så fungerar de](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo serialisering](https://github.com/EsotericSoftware/kryo)
