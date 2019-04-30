---
title: Spark-strömning i Azure HDInsight
description: Hur du använder Spark Streaming program på HDInsight Spark-kluster.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/11/2019
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 19d77d4aa49008232a01cd3ac2761a796505a35c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098226"
---
# <a name="overview-of-apache-spark-streaming"></a>Översikt över Apache Spark-strömning

[Apache Spark](https://spark.apache.org/) Streaming tillhandahåller data för strömbearbetning i HDInsight Spark-kluster med en garanti för att ange någon händelse bearbetas exakt en gång, även om ett nodfel inträffar. Ett Spark-Stream är en tidskrävande jobb som tar emot inkommande data från en mängd olika källor, inklusive Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ ](http://zeromq.org/), raw TCP sockets, eller från övervakning [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) filsystem. Till skillnad från en process som enbart händelsedriven, ett Spark-Stream slår ihop indata i tidsfönster, till exempel en sektor i 2 sekunder, och sedan transformeringar varje batch med hjälp av kartan, minska, ansluta till och extrahera operations. Spark Stream skriver sedan transformerade data till filsystem, databaser, instrumentpaneler och i konsolen.

![Stream bearbetning med HDInsight och Spark-strömning](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streaming program måste vänta en bråkdel av en sekund att samla in varje *mindre batchar* händelser innan de skickas som batch för bearbetning. Däremot bearbetar ett händelsedrivna program varje händelse omedelbart. Spark Streaming svarstiden är normalt under några sekunder. Fördelarna med mindre batchar-metoden är mer effektiv bearbetning och enklare sammanställd beräkningar.

## <a name="introducing-the-dstream"></a>Introduktion till DStream

Spark Streaming representerar en ständig ström med inkommande data med hjälp av en *diskretiserade stream* kallas en DStream. En DStream kan skapas från inmatningskällor, till exempel Händelsehubbar eller Kafka eller genom att använda omformningar på en annan DStream.

En DStream innebär en abstraktion ovanpå raw händelsedata. 

Börja med en enda händelse, anta att en läsning från en ansluten termostat temperatur. När den här händelsen når programmet Spark Streaming, lagras händelsen på ett tillförlitligt sätt, där de replikeras på flera noder. Den här feltolerans säkerställer att fel på någon nod inte resulterar i förlust av din händelse. Spark core använder en datastruktur som distribuerar data över flera noder i klustret, där varje nod Allmänt upprätthåller sin egen data i minnet för bästa prestanda. Den här datastrukturen kallas en *elastiska distribuerade datauppsättning* (RDD).

Varje RDD representerar händelser som samlas in via en användardefinierad tidsram som kallas den *batch intervall*. Som det ska gå att varje batch-intervall, skapas en ny RDD som innehåller alla data från det här intervallet. Kontinuerlig uppsättning Rdd samlas in till en DStream. Till exempel om batch-intervallet är en sekund lång tid, genererar dina DStream en batch varje andra som innehåller en RDD som innehåller alla data som matas in under den andra. Vid bearbetning av DStream visas temperatur händelsen på en av dessa batchar. Ett program med Spark Streaming bearbetar batchar som innehåller händelserna och slutligen fungerar på data som lagras i varje RDD.

![Exempel DStream med temperatur händelser ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktur för ett program med Spark Streaming

Ett program med Spark Streaming är en tidskrävande program som tar emot data från källor för inmatning, tillämpar transformationer för att bearbeta data och sedan skickar data till en eller flera mål. Struktur för ett program med Spark Streaming har en statisk och en dynamisk del. Statiska del definierar där data kommer från vilka bearbetning för att göra på data, och där resultaten ska gå. Den dynamiska delen körs programmet på obestämd tid, väntar på en stoppsignal.

Till exempel följande enkla program tar emot en rad med text via en TCP-socket och räknar hur många gånger varje ord visas.

### <a name="define-the-application"></a>Definiera programmet

Logik programdefinition består av fyra steg:

1. Skapa en StreamingContext.
2. Skapa en DStream från StreamingContext.
3. Använda omformningar på DStream.
4. Matar ut resultaten.

Den här definitionen är statisk och inga data har bearbetats tills du kör programmet.

#### <a name="create-a-streamingcontext"></a>Skapa en StreamingContext
Skapa en StreamingContext från SparkContext som pekar på ditt kluster. När du skapar en StreamingContext kan ange du storlek på batch i sekunder, till exempel:

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Skapa en DStream

Skapa en inkommande DStream för din Indatakällan med StreamingContext-instans. I det här fallet bevakar programmet för utseendet på nya filer i standardlagring kopplade till HDInsight-klustret.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Använda omformningar

Du kan implementera bearbetningen genom att använda omformningar på DStream. Det här programmet tar emot en rad med text vid ett tillfälle från filen, delar upp varje rad i ord och använder sedan ett map-reduce mönster för att räkna antalet gånger varje ord visas.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Utdataresultat

Skicka omvandling resultatet ut till mål-system genom att använda output-åtgärder. I det här fallet är resultatet av varje körning via beräkningen ut i konsolens utdata.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Köra programmet
Starta direktuppspelning programmet och kör tills en uppsägning signal tas emot.

```
ssc.start()
ssc.awaitTermination()
```

Information om Spark Stream API, tillsammans med de händelsekällor och transformationer output-åtgärder som stöds finns i [Apache Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Följande exempelprogrammet är självständig, så att du kan köra den inuti en [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). Det här exemplet skapar en fingerad datakälla i klassen DummySource som matar ut värdet för en räknare och den aktuella tiden i millisekunder var femte sekund. Ett nytt StreamingContext objekt har ett batch-intervall på 30 sekunder. Varje gång en batch skapas strömmande programmet undersöker RDD produceras, konverterar RDD till en Spark DataFrame och skapar en temporär tabell över nu när dataramen.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()
```

Vänta ungefär 30 sekunder efter start av programmet som ovan.  Du kan sedan fråga DataFrame regelbundet för att se den aktuella uppsättningen värden som finns i batchen, till exempel med hjälp av den här SQL-frågan:

```sql
%%sql
SELECT * FROM demo_numbers
```

Resultatet ser ut så här:

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Det finns sex värden eftersom DummySource skapar ett värde var femte sekund och programmet genererar en batch med 30 sekunders mellanrum.

## <a name="sliding-windows"></a>Glidande windows

Du kan använda för att utföra sammanställd beräkningar på dina DStream under en tidsperiod, till exempel för att få en medeltemperaturen under de senaste två sekunderna den *skjutfönster* åtgärder som ingår i Spark Streaming. Ett skjutfönster har en varaktighet (fönstret längden) och intervallet då innehållet i fönstret är utvärderas (bild-intervall).

Glidande windows kan överlappa, till exempel kan du definiera ett fönster med en längd på två sekunder, som bilder var en sekund. Det här innebär att varje gång du utför en beräkning för aggregering, fönstret innehåller data från den senaste en andra föregående fönster samt nya data i nästa en sekund.

![Exempel fönstret inledande med temperatur händelser](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Exempel-fönster med temperatur händelser efter glidande](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

I följande exempel uppdateras den kod som använder DummySource, att samla in batcharna i ett fönster med en varaktighet på en minut och en bild i en minut.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
} 

// Start the stream processing
ssc.start()
```

Det finns 12 transaktioner - sex transaktioner från var och en av de två batcharna som samlas in i fönstret efter den första minuten.

| value | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Glidande fönstrets funktioner tillgängliga i Spark Streaming API är fönstret, countByWindow, reduceByWindow och countByValueAndWindow. Mer information om dessa funktioner finns [transformationer i DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Kontrollpunkter

För att leverera elasticitet och feltolerans, Spark Streaming förlitar sig på kontrollpunkter för att säkerställa att bearbetning av dataströmmen kan fortsätta utan problem, som även körs vid nodfel. I HDInsight skapar Spark kontrollpunkter till beständig lagring (Azure Storage eller Data Lake Storage). Dessa kontrollpunkter lagra metadata om strömmande programmet till exempel konfiguration, åtgärder som definierats av programmet och eventuella batchar som placerats i kö men ännu inte behandlats. I vissa fall kan med kontrollpunkterna även spara data i rdd-datauppsättningar till att snabbt återskapa tillståndet för data från vad som finns i rdd-datauppsättningar som hanteras av Spark.

## <a name="deploying-spark-streaming-applications"></a>Distribuera Spark Streaming program

Du vanligtvis skapa ett Spark Streaming-program lokalt i en JAR-fil och sedan distribuera den till Apache Spark på HDInsight genom att kopiera JAR-filen till standardlagring kopplat till ditt HDInsight-kluster. Du kan starta programmet med LIVY REST API: erna från ditt kluster med hjälp av en POST-åtgärd. Brödtexten i INLÄGGET innehåller ett JSON-dokument som innehåller sökvägen till din JAR, namnet på klassen vars main-metoden definierar och kör programmet strömmande och eventuellt resurskraven för jobbet (till exempel antalet executors, minne och kärnor) , och kräver att alla konfigurationsinställningar programkoden.

![Distribuera ett Spark Streaming-program](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Status för alla program kan också kontrolleras med en GET-begäran mot en LIVY-slutpunkt. Slutligen kan avsluta du ett program som körs genom att utfärda en DELETE-begäran mot slutpunkten LIVY. Mer information om LIVY-API: et finns [fjärrstyrda jobb med Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Apache Spark-kluster i HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark-strömning Programmeringsguide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Starta Apache Spark-jobb via fjärranslutning med Apache LIVY](apache-spark-livy-rest-interface.md)
