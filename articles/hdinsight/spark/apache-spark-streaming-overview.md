---
title: Spark Streaming i Azure HDInsight
description: Så här använder du Apache Spark Streaming-program på HDInsight Spark-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 521d72642a27995d096402a4ca0e4af632b0788c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406268"
---
# <a name="overview-of-apache-spark-streaming"></a>Översikt över Apache Spark Streaming

[Apache gnista](https://spark.apache.org/) Direktuppspelning ger dataströmbearbetning på HDInsight Spark-kluster, med en garanti för att alla indatahändelser bearbetas exakt en gång, även om ett nodfel inträffar. En Spark Stream är ett tidskrävande jobb som tar emot indata från en mängd olika källor, inklusive Azure Event Hubs, en Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), rå TCP-sockets eller från övervakning [av Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) YARN-filsystem. Till skillnad från en enbart händelsedriven process grupperar en Spark Stream indata i tidsfönster, till exempel ett 2-sekunderssegment, och omvandlar sedan varje grupp data med hjälp av kart-, reduce-, kopplings- och utdragsåtgärder. Spark Stream skriver sedan ut transformerade data till filsystem, databaser, instrumentpaneler och konsolen.

![Stream bearbetning med HDInsight och Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streaming-program måste vänta en bråkdel av en sekund för att samla in varje *mikrobatch* av händelser innan du skickar batchen på för bearbetning. Däremot bearbetar ett händelsedrivet program varje händelse omedelbart. Svarstiden för Spark Streaming är vanligtvis under några sekunder. Fördelarna med mikrobatsatsmetoden är effektivare databehandling och enklare aggregerade beräkningar.

## <a name="introducing-the-dstream"></a>Vi presenterar DStream

Spark Streaming representerar en kontinuerlig ström av inkommande data med hjälp av en *diskret ström* som kallas DStream. En DStream kan skapas från indatakällor som Event Hubs eller Kafka, eller genom att tillämpa omvandlingar på en annan DStream.

En DStream ger ett abstraktionslager ovanpå råhändelsedata.

Börja med en enda händelse, säger en temperaturavläsning från en ansluten termostat. När den här händelsen anländer till ditt Spark Streaming-program lagras händelsen på ett tillförlitligt sätt, där den replikeras på flera noder. Den här feltoleransen säkerställer att felet för en enskild nod inte leder till förlust av din händelse. Spark-kärnan använder en datastruktur som distribuerar data över flera noder i klustret, där varje nod i allmänhet underhåller sina egna data i minnet för bästa prestanda. Den här datastrukturen kallas för en *fjädrande distribuerad datauppsättning* (RDD).

Varje RDD representerar händelser som samlats in under en användardefinierad tidsram som kallas *batchintervallet*. När varje batchintervall förflutit produceras en ny RDD som innehåller alla data från det intervallet. Den kontinuerliga uppsättningen RDD:er samlas in i en DStream. Om batchintervallet till exempel är en sekund långt avger din DStream en batch varje sekund som innehåller en RDD som innehåller alla data som intas under den sekunden. Vid bearbetning av DStream visas temperaturhändelsen i en av dessa batchar. Ett Spark Streaming-program bearbetar de batchar som innehåller händelserna och slutligen fungerar på data som lagras i varje RDD.

![Exempel på DStream med temperaturhändelser](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktur för ett Spark Streaming-program

Ett Spark Streaming-program är ett tidskrävande program som tar emot data från intvaltskällor, tillämpar omvandlingar för att bearbeta data och skickar sedan ut data till en eller flera destinationer. Strukturen för ett Spark Streaming-program har en statisk del och en dynamisk del. Den statiska delen definierar var data kommer ifrån, vilken bearbetning som ska ske på data och vart resultaten ska gå. Den dynamiska delen kör programmet på obestämd tid och väntar på en stoppsignal.

Följande enkla program tar till exempel emot en textrad över en TCP-socket och räknar antalet gånger varje ord visas.

### <a name="define-the-application"></a>Definiera programmet

Programlogikens definition har fyra steg:

1. Skapa en StreamingContext.
2. Skapa en DStream från StreamingContext.
3. Tillämpa omvandlingar på DStream.
4. Utdata resultaten.

Den här definitionen är statisk och inga data bearbetas förrän du kör programmet.

#### <a name="create-a-streamingcontext"></a>Skapa en StreamingContext

Skapa en StreamingContext från SparkContext som pekar på klustret. När du skapar en StreamingContext anger du storleken på batchen på några sekunder, till exempel:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Skapa en DStream

Med StreamingContext-instansen skapar du en indata-DStream för indatakällan. I det här fallet tittar programmet efter utseende av nya filer i standardlagringen som är ansluten till HDInsight-klustret.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Använda omvandlingar

Du implementerar bearbetningen genom att använda omvandlingar på DStream. Det här programmet tar emot en textrad i taget från filen, delar varje rad i ord och använder sedan ett kartreduceringsmönster för att räkna antalet gånger varje ord visas.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Utdataresultat

Skicka ut omvandlingsresultaten till målsystemen genom att använda utdataåtgärder. I det här fallet skrivs resultatet av varje körning genom beräkningen ut i konsolens utdata.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Köra appen

Starta programmet för direktuppspelning och kör tills en uppsägningssignal tas emot.

```
ssc.start()
ssc.awaitTermination()
```

Mer information om Spark Stream-API:et, tillsammans med händelsekällor, omvandlingar och utdataåtgärder som stöds, finns i [Apache Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Följande exempelprogram är fristående, så att du kan köra det i en [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). I det här exemplet skapas en modelldatakälla i klassen DummySource som matar ut värdet för en räknare och den aktuella tiden i millisekunder var femte sekund. Ett nytt StreamingContext-objekt har ett batchintervall på 30 sekunder. Varje gång en batch skapas undersöker programmet för direktuppspelning den producerade RDD-webbplatsen, konverterar FJÄRRSKRIVBORDSDA till en Spark DataFrame och skapar en tillfällig tabell över DataFrame.

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

Vänta i ca 30 sekunder efter att programmet har startats ovan.  Sedan kan du fråga DataFrame regelbundet för att se den aktuella uppsättningen värden som finns i batchen, till exempel med den här SQL-frågan:

```sql
%%sql
SELECT * FROM demo_numbers
```

Den resulterande utdata ser ut så här:

| värde | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Det finns sex värden, eftersom DummySource skapar ett värde var 5:e sekund och programmet avger en batch var 30:e sekund.

## <a name="sliding-windows"></a>Skjutbara fönster

Om du vill utföra aggregerade beräkningar på din DStream under en viss tidsperiod, till exempel för att få en medeltemperatur under de senaste två sekunderna, kan du använda de *skjutfönsteråtgärder* som ingår i Spark Streaming. Ett skjutfönster har en varaktighet (fönsterlängden) och det intervall under vilket fönstrets innehåll utvärderas (bildintervallet).

Skjutbara fönster kan överlappa varandra, du kan till exempel definiera ett fönster med en längd på två sekunder, som glider varannan sekund. Det innebär att varje gång du utför en aggregeringsberäkning kommer fönstret att innehålla data från den sista sekunden i föregående fönster samt alla nya data under nästa sekund.

![Exempel på inledande fönster med temperaturhändelser](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Exempel fönster med temperaturhändelser efter glidning](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

I följande exempel uppdateras koden som använder DummySource för att samla in batcharna i ett fönster med en minuts varaktighet och en minuts bild.

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

Efter den första minuten finns det 12 poster - sex poster från var och en av de två batchar som samlats in i fönstret.

| värde | time |
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

De skjutbara fönsterfunktioner som är tillgängliga i Spark Streaming API inkluderar fönster, countByWindow, reduceByWindow och countByValueAndWindow. Mer information om dessa funktioner finns i [Omvandlingar på DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Kontrollpunkter

För att leverera återhämtning och feltolerans förlitar sig Spark Streaming på kontrollpunkter för att säkerställa att dataströmbearbetning kan fortsätta oavbrutet, även med tanke på nodfel. I HDInsight skapar Spark kontrollpunkter till varaktig lagring (Azure Storage eller Data Lake Storage). Dessa kontrollpunkter lagrar metadata om direktuppspelningsprogrammet, till exempel konfigurationen, de åtgärder som definierats av programmet och alla batchar som har köats men ännu inte bearbetats. I vissa fall kommer kontrollpunkterna också att innehålla spara data i RDDs för att snabbare återskapa tillståndet för data från vad som finns i RDDs hanteras av Spark.

## <a name="deploying-spark-streaming-applications"></a>Distribuera Spark Streaming-program

Du skapar vanligtvis ett Spark Streaming-program lokalt i en JAR-fil och distribuerar det sedan till Spark på HDInsight genom att kopiera JAR-filen till standardlagringen som är ansluten till ditt HDInsight-kluster. Du kan starta programmet med LIVY REST API:er som är tillgängliga från klustret med hjälp av en POST-åtgärd. Brödtexten i POST innehåller ett JSON-dokument som ger sökvägen till jar:n, namnet på den klass vars huvudmetod definierar och kör direktuppspelningsprogrammet och eventuellt resurskraven för jobbet (till exempel antalet utförare, minne och kärnor) och alla konfigurationsinställningar som programkoden kräver.

![Distribuera ett Spark Streaming-program](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Status för alla program kan också kontrolleras med en GET-begäran mot en LIVY-slutpunkt. Slutligen kan du avsluta ett program som körs genom att utfärda en DELETE-begäran mot LIVY-slutpunkten. Mer information om LIVY API finns i [Fjärrjobb med Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Apache Spark-kluster i HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmeringsguide för Apache Spark-strömning](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Starta Apache Spark jobb på distans med Apache LIVY](apache-spark-livy-rest-interface.md)
