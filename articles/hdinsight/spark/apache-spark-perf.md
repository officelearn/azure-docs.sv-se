---
title: Optimera prestanda - Azure HDInsight Spark jobb | Microsoft Docs
description: "Visar vanliga strategier för bästa prestanda för Spark-kluster."
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 64ddb70f071a9fadc6fef64dcd3506c6d6255481
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="optimize-spark-jobs"></a>Optimera Spark-jobb

Lär dig hur du optimerar Spark klusterkonfigurationen för en viss arbetsbelastning.  De vanligaste utmaningen är minnesbelastning på grund av felaktiga konfigurationer (särskilt fel storlek executors), långvariga åtgärder och aktiviteter som resulterar i kartesiska åtgärder. Du kan påskynda jobb med lämpliga cachelagring och genom att [data förskjutning](#optimize-joins-and-shuffles). För bästa prestanda, övervaka och granska långvariga och tillämpningen av resursen Spark jobbet körningar.

I följande avsnitt beskrivs vanliga Spark jobbet optimeringar och rekommendationer.

## <a name="choose-the-data-abstraction"></a>Välj data abstraction

Väck 1.x använder RDDs abstrakt data och Spark 2.x introduceras DataFrames och datauppsättningar. Överväg följande eller nackdelar:

* **DataFrames**
    * Bäst i de flesta fall
    * Ger optimeringsnivån via Catalyst
    * Kodgenereringen för hela steg
    * Direkt minnesåtkomst
    * Liten overhead skräpinsamling (GC)
    * Inte som utvecklare anpassad som datauppsättningar, eftersom det inte finns några kompileringen kontrollerar domän objektet programmering
* **Datauppsättningar**
    * Bra i komplexa ETL pipelines där prestandapåverkan tillåts
    * Inte bra i aggregeringar där prestandapåverkan kan vara betydande
    * Ger optimeringsnivån via Catalyst
    * Utvecklare eget genom att tillhandahålla objektet programmeringsspråk och kompileringstid domänkontrollen
    * Lägger till serialisering/deserialisering kostnader
    * Hög GC kostnader
    * Bryter hela steg kodgenerering
* **RDDs**
    * I Spark 2.x kan du inte behöver använda RDDs, om du inte behöver skapa en ny anpassad RDD
    * Ingen fråga optimering via Catalyst
    * Inga hela steg kodgenerering
    * Hög GC kostnader
    * Måste använda Spark 1.x äldre API: er

## <a name="use-optimal-data-format"></a>Använd optimala dataformat

Spark stöder många format, till exempel csv, json, xml, parkettgolv, orc och avro. Spark kan utökas till att hantera många fler format med externa datakällor - mer information finns i [Väck paket](https://spark-packages.org).

Formatet för bästa prestanda är parkettgolv med *snygga komprimering*, vilket är standard i Spark 2.x. Parkettgolv lagrar data i kolumnformat och är optimerad i Spark.

## <a name="select-default-storage"></a>Välj standardlagring

När du skapar ett nytt Spark-kluster har du möjlighet att välja Azure Blob Storage eller Azure Data Lake Store som standard klusterlagringen. Båda alternativen får du fördelen med långsiktig lagring för tillfälliga kluster, så att dina data inte hämta bort automatiskt när du tar bort klustret. Du kan skapa ett tillfälligt kluster och fortfarande komma åt dina data.

| Lagringstyp | Filsystem | Hastighet | Tillfälligt | Användningsfall |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Ja | Tillfälligt kluster |
| Azure Data Lake Store | **ADL:**//url/ | **Snabbare** | Ja | Tillfälligt kluster |
| Lokala HDFS | **hdfs:**//url/ | **Snabbaste** | Nej | Interaktiva 24/7-kluster |

## <a name="use-the-cache"></a>Använd cache

Spark innehåller sin egen interna cachelagring mekanismer som kan användas via olika metoder som `.persist()`, `.cache()`, och `CACHE TABLE`. Den här inbyggda cachelagring är effektivt med små datauppsättningar samt som ETL pipelines där du behöver cache mellanresultat. Dock fungerar Spark interna cachelagring för närvarande inte bra med partitionering, eftersom en cachelagrad tabell inte behålla partitionering data. En mer allmän och tillförlitlig cachelagring tekniken är *lagring layer caching*.

* Intern Spark cachelagring (rekommenderas inte)
    * Praktiskt för små datauppsättningar.
    * Matchar inte de fungerar med partitionering, som kan ändras i framtida versioner av Spark.

* Lagring nivån cachelagring (rekommenderas)
    * Kan implementeras med hjälp av [Alluxio](http://www.alluxio.org/).
    * Använder i minnet och cachelagring i SSD.

* Lokala HDFS (rekommenderas)
    * `hdfs://mycluster`sökvägen.
    * Använder SSD cachelagring.
    * Cachelagrade data går förlorade när du tar bort klustret, som kräver en återskapning av en cache.

## <a name="use-memory-efficiently"></a>Använda minnet effektivt

Spark fungerar genom att placera data i minnet, så att hantera Minnesresurserna är en viktig del av optimering av körningen av Spark jobb.  Det finns flera metoder du kan använda för att använda ditt kluster minne effektivt.

* Föredrar mindre datapartitioner för och ta hänsyn till datastorleken, typer och distribution i din strategi för partitionering.
* Överväg att nyare, effektivare [Kryo dataserialisering](https://github.com/EsotericSoftware/kryo), i stället för standard Java-serialisering.
* Föredrar att använda YARN, som användarfunktioner `spark-submit` av batch.
* Övervaka och justera Spark-konfigurationsinställningar.

Referens visas Spark minne struktur och vissa viktiga utföraren minne parametrar i nästa bild.

### <a name="spark-memory-considerations"></a>Överväganden för Spark-minne

Om du använder YARN styr YARN maximala summan av minne som används av alla behållare på varje nod i Spark.  Följande diagram visar viktiga objekt och deras relationer.

![YARN Spark minneshantering](./media/apache-spark-perf/yarn-spark-memory.png)

För att åtgärda out-of-minne, meddelanden, gör du:

* Granska DAG Management Shuffles. Minska genom att mappa sida reducting, partitionera före (eller bucketize) källdata, maximera enda shuffles och minska mängden data som skickas.
* Föredrar `ReduceByKey` med fast minnesgränsen till `GroupByKey`, som innehåller aggregeringar, fönsterhantering och andra funktioner, men den har ann unbounded minnesgränsen.
* Föredrar `TreeReduce`, som har mer arbete på executors eller partitioner, till `Reduce`, vilket gör allt arbete på drivrutinen.
* Utnyttja DataFrames snarare än RDD-objekt på lägre nivå.
* Skapa ComplexTypes som kapslar in åtgärder, till exempel ”Top N” olika aggregeringar eller fönsterhantering åtgärder.

## <a name="optimize-data-serialization"></a>Optimera dataserialisering

Spark jobb distribueras så lämplig serialisering är viktigt för bästa prestanda.  Det finns två alternativ för serialisering för Spark:

* Java-serialisering är standardinställningen.
* Kryo serialisering är ett nyare format och kan resultera i snabbare och mer kompakt serialisering än Java.  Kryo kräver att du registrerar klasserna i ditt program och den stöder ännu inte alla typer som kan serialiseras.

## <a name="use-bucketing"></a>Använd bucketing

Bucketing liknar Datapartitionering, men varje bucket kan innehålla en uppsättning kolumnvärdena i stället för bara en. Bucketing fungerar bra för partitionering på (i miljontals eller mer) har många värden, till exempel produkt-ID: n. En bucket bestäms av hash-bucket-nyckeln för raden. Bucketgrupperade tabeller erbjuder unika optimeringar eftersom de lagras metadata om hur de bucketgrupperade och sorteras.

Vissa avancerade bucketing funktioner är:

* Fråga optimering baserat på bucketing metainformation
* Optimerad aggregeringar
* Optimerad kopplingar

Du kan använda partitionering och bucketing på samma gång.

## <a name="optimize-joins-and-shuffles"></a>Optimera kopplingar och shuffles

Om du har långsam jobb på en koppling eller blanda orsaken är troligen *data förskjutning*, vilket är asymmetrisk i dina jobbdata. Till exempel en karta jobbet Ta 20 sekunder, men kör ett jobb där data är ansluten eller blandad tar timmar.   Om du vill åtgärda skeva data bör du salt hela nyckeln eller använda en *isolerade salt* för endast en delmängd av nycklar.  Om du använder en isolerad salt bör du ytterligare filter för att isolera en delmängd av saltat nycklar i kartan kopplingar. Ett annat alternativ är att införa en bucket-kolumn och sammanställa före i buckets först.

En annan faktor som orsakar långsam kopplingar kan vara kopplingstyp. Som standard Väck använder den `SortMerge` kopplingstyp. Den här typen av koppling lämpar sig bäst för stora datamängder, men är annars beräkningsmässigt dyr eftersom det måste först sortera till vänster och höger data innan du slå ihop dem.

En `Broadcast` koppling är bäst för mindre datamängder, eller om en sida i kopplingen är mycket mindre än den andra sidan. Den här typen av koppling sänder en sida till alla executors och kräver mer minne för sändning i allmänhet.

Du kan ändra kopplingstyp i din konfiguration genom att ange `spark.sql.autoBroadcastJoinThreshold`, eller ange en join-tips med DataFrame APIs (`dataframe.join(broadcast(df2))`).

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

Om du använder bucketgrupperade tabeller, så att du har en kopplingstyp, tredje den `Merge` koppling. En korrekt före partitionerade och före sorterade dataset hoppar över dyra sortera fas från en `SortMerge` koppling.

Ordningen för kopplingar är viktig, särskilt i mer komplexa frågor. Börja med de flesta selektiv kopplingar. Dessutom flytta kopplingar som ökar antalet rader efter aggregeringar när det är möjligt.

Om du vill hantera parallellitet, särskilt när det gäller kartesiska kopplingar, kan du lägga till kapslade strukturer fönsterhantering, och kanske hoppa över en eller flera steg i Spark-jobbet.

## <a name="customize-cluster-configuration"></a>Anpassa klusterkonfigurationen

Beroende på din Spark-klusterarbetsbelastning, kan du bestämma att en icke-förvalt Spark konfiguration skulle leda till flera optimerade Spark jobbkörningen.  Utföra benchmark testning med exempel arbetsbelastningar för att verifiera alla klusterkonfigurationer som inte är standard.

Här följer några vanliga parametrar som du kan justera:

* `--num-executors`Anger antalet executors.
* `--executor-cores`Anger antalet kärnor för varje utförare. Normalt bör du ha middle-sized executors som andra processer använder vissa av det tillgängliga minnet.
* `--executor-memory`Anger minnesstorleken för varje utföraren som styr heapstorlek på YARN. Du bör lämna minne för körning av arbetet.

### <a name="select-the-correct-executor-size"></a>Välj rätt utföraren storlek

När du bestämmer utföraren konfigurationen, Överväg Java skräp samling (GC) kostnader.

* Faktorer att minska utföraren storlek:
    1. Minska storleken på heap nedan 32 GB för att hålla GC administration < 10%.
    2. Minska antalet kärnor för att hålla GC administration < 10%.

* Faktorer att öka utföraren storlek:
    1. Minska kommunikation mellan executors försämras.
    2. Minska antalet öppna anslutningar mellan executors (N2) på större kluster (> 100 executors).
    3. Öka stackstorleken kan utföra vissa uppgifter.
    4. Valfritt: Minska per utföraren minnesanvändningen.
    5. Valfritt: Öka användningen och samtidighet genom överteckning CPU.

Som en allmän tumregel när du väljer utföraren storlek:
    
1. Börja med 30 GB per utföraren och fördela tillgängliga datorn kärnor.
2. Öka antalet utföraren kärnor för större kluster (> 100 executors).
3. Öka eller minska baseras både på utvärderingsversion körs och på föregående faktorer, till exempel GC kostnader.

Tänk på följande när du kör samtidiga frågor:

1. Börja med 30 GB per utföraren och alla datorn kärnor.
2. Skapa flera parallella Spark-program med överteckning CPU (cirka 30% latens improvement).
3. Distribuera frågor mellan parallella applikationer.
4. Öka eller minska baseras både på utvärderingsversion körs och på föregående faktorer, till exempel GC kostnader.

Övervaka frågeprestandan för avvikare eller andra problem med prestanda genom att titta på tidslinjevyn, SQL-diagram, jobb statistik och så vidare. En eller några av executors är ibland långsammare än andra och uppgifter som tar mycket längre tid att köra. Detta inträffar ofta större kluster (> 30 noder). I det här fallet dela arbete i ett större antal uppgifter så Schemaläggaren kan kompensera för långsam uppgifter. Till exempel ha minst två gånger så många aktiviteter som antalet utföraren kärnor i programmet. Du kan också aktivera spekulativ körningen av uppgifter med `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optimera jobbkörningen

* Cachelagra vid behov, till exempel om du använder data två gånger och sedan cachelagra den.
* Broadcast variabler att alla executors. Variablerna serialiseras bara en gång, vilket resulterar i snabbare sökningar.
* Använd trådpoolen på drivrutinen, vilket resulterar i snabbare för många uppgifter.

Övervaka dina jobb som körs regelbundet för prestandaproblem. Om du behöver större insyn i vissa problem, bör du något av följande prestanda profilering verktyg:

* [Intel PAL verktyget](https://github.com/intel-hadoop/PAT) övervakar CPU, lagring och nätverksanvändning för bandbredd.
* [Oracle Java 8 UPPDRAGSKONTROLL](http://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profiler Spark och utföraren kod.

Nyckeln till Spark 2.x frågeprestanda är volfram motorn, som beror på hela steg kodgenerering. I vissa fall kan hela steg kodgenerering inaktiveras. Till exempel om du använder en icke avstängningsbara typ (`string`) i uttrycket aggregering `SortAggregate` visas i stället för `HashAggregate`. För bättre prestanda, till exempel försöka med följande och sedan återaktivera kodgenerering:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Spark-jobb som körs på Azure HDInsight](apache-spark-job-debugging.md)
* [Hantera resurser för ett Spark-kluster i HDInsight](apache-spark-resource-manager.md)
* [Använda Spark REST API för att skicka remote jobb till ett Spark-kluster](apache-spark-livy-rest-interface.md)
* [Justera Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Hur jobb så att faktiskt finjustera din Spark fungerar de](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo serialisering](https://github.com/EsotericSoftware/kryo)
