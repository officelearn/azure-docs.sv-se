---
title: "Vad är Spark Streaming i Azure HDInsight | Microsoft Docs"
description: "Hur du använder Spark Streaming program på HDInsight Spark-kluster."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: raghavmohan
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: ramoha
ms.openlocfilehash: 9debf78480e6c77ab23581754350fb95f34f8fc4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="overview-of-spark-streaming"></a>Översikt över Spark Streaming

Spark Streaming ger dataströmmen databehandling på HDInsight Spark kluster med en garanti för att alla inkommande händelser bearbetas exakt en gång, även om ett nodfel inträffar. En dataström med Spark är en tidskrävande jobb som tar emot indata från en mängd olika källor, till exempel Azure Event Hubs, ett Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ raw TCP-sockets eller övervakning HDFS-filsystem. Till skillnad från en enbart händelsedriven process, en dataström med Spark batchar indata i tidsfönster, till exempel ett 2 sekunder segment och omvandlar varje batch data med hjälp av karta, minska, ansluta och extrahera operations. Sedan skriver den omvandlade data till filsystem, databaser, instrumentpaneler och konsolen.

![Bearbetning med HDInsight och Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streaming program måste vänta några bråkdelar av en sekund att samla in varje *micro batch* händelser innan du skickar den batchen för bearbetning. Däremot behandlar ett händelsedriven program varje händelse direkt. Spark Streaming svarstiden är normalt under några sekunder. Fördelar med micro batch-metoden är effektivare databehandling och enklare sammanställd beräkningar.

## <a name="introducing-the-dstream"></a>Introduktion till DStream

Spark Streaming representerar en ständig ström av inkommande data med hjälp av en *diskretiserade dataströmmen* kallas en DStream. En DStream kan skapas från indatakällor som Händelsehubbar eller Kafka, eller genom att använda en annan DStream transformationer.

En DStream tillhandahåller ett Abstraktionslager ovanpå rådata händelsedata. 

Börja med en enskild händelse, säg temperaturen läsning från en ansluten termostat. När den här händelsen når programmet Spark Streaming, händelsen lagras på ett tillförlitligt sätt, det vill säga de replikeras på flera noder. Den här feltolerans säkerställer att fel på en enskild nod inte resulterar i förlust av din händelse. Spark core använder en datastruktur som distribuerar data över flera noder i klustret, där varje nod vanligtvis underhåller egna data i minnet för bästa prestanda. Den här datastruktur kallas en *flexibel distribuerade dataset* (RDD).

Varje RDD representerar händelser som samlas in via en användardefinierad tidsram som kallas batch-intervall. Som det ska gå att varje batch-intervall, skapas en ny RDD som innehåller alla data från intervallet. Kontinuerlig uppsättning RDDs samlas in till en DStream. Till exempel om batch-intervallet är en sekund långt, skickar din DStream en batch var andra som innehåller en RDD som innehåller de data som inhämtas under den andra. Vid bearbetning av DStream visas temperatur-händelse i något av dessa grupper. Ett program med Spark Streaming bearbetar batchar som innehåller händelserna och slutligen fungerar på data som lagras i varje RDD.

![Exempel DStream med temperatur händelser ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktur för ett Spark Streaming program
Ett Spark Streaming program är en tidskrävande program som tar emot data från källor infognings gäller omvandlingar för att bearbeta data och sedan skickar data till en eller flera mål. Struktur för ett program med Spark Streaming har två delar. Först måste du definiera där data hämtas från vilken bearbetning för att göra data, och där resultaten ska gå. Andra kan köra du programmet på obestämd tid väntar på att en stoppsignal.

Till exempel är ett enkelt program som tar emot en textrad via en TCP-socket och räknar antalet gånger varje ord visas här.

### <a name="define-the-application"></a>Definiera programmet

Logik-programdefinition består av fyra steg:

1. Skapa en StreamingContext.
2. Skapa en DStream från StreamingContext.
3. Använda omformningar på DStream.
4. Skriver resultatet.

Den här definitionen är statisk och inga data har bearbetats förrän du kör programmet.

#### <a name="create-a-streamingcontext"></a>Skapa en StreamingContext
Skapa en StreamingContext från SparkContext som pekar på ditt kluster. När du skapar en StreamingContext kan ange du storlek på batch i sekunder, till exempel:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Skapa en DStream
Skapa en inkommande DStream för din indatakälla StreamingContext-instans som du skapade. I det här fallet tittar vi för utseendet på nya filer i standard-lagringsutrymme som är anslutet till HDInsight-klustret.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Använda omformningar
Du kan implementera bearbetning genom att använda omformningar på DStream. Appen tar emot en rad med text i taget från filen delas varje rad i ord och använder sedan ett map-reduce mönster för att räkna antalet gånger varje ord visas.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Resultatet
Push-transformation resultatet till mål-system genom att använda o-åtgärder. I det här fallet visar vi resultatet av varje kör beräkningen i konsolens utdata.

    wordCounts.print()

### <a name="run-the-application"></a>Köra programmet
Starta strömning programmet och kör tills en Avslutningssignal tas emot.

    ssc.start()            
    ssc.awaitTermination()

Mer information om API: et för Spark dataströmmen tillsammans med händelsekällor, omvandlingar och o-åtgärder stöder Se [Spark Streaming Programmeringsguide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Följande exempelprogrammet är självständigt, så att du kan köra den inuti en [Jupyter-anteckningsbok](apache-spark-jupyter-notebook-kernels.md). I det här exemplet skapar vi en fingerad datakälla i klassen DummySource som matar ut värdet för en räknare och den aktuella tiden i millisekunder var femte sekund. Vi skapar ett nytt StreamingContext-objekt som har ett batch-intervall på 30 sekunder. Varje gång en batch skapas undersöker RDD producerade den, konverterar den till en Spark DataFrame och skapar en tillfällig tabell över DataFrame.

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

Vi kan sedan fråga DataFrame regelbundet för att se den aktuella uppsättningen värden som finns i batchen. I detta fall kan använda vi följande SQL-fråga:

    %%sql
    SELECT * FROM demo_numbers

Resultatet ser ut ungefär så här:

| värde | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Vi räknar med att finns sex värden eftersom DummySource skapar ett värde var femte sekund och vi Generera en batch med 30 sekunders mellanrum.

## <a name="sliding-windows"></a>Glidande windows
Du kan använda glidande fönstret åtgärderna som ingår i Spark Streaming om du vill utföra sammanställd beräkningar på din DStream under vissa tidsperiod, till exempel för att hämta en medeltemperaturen under de senaste 2 sekunderna. Ett skjutfönster definieras som har en varaktighet (fönstret längd) och den period under vilken fönstrets innehåll är utvärderas (bild intervall).

Dessa glidande windows kan överlappa, till exempel kan du definiera ett fönster med en längd på 2 sekunder som bilder varje sekund. Det innebär varje gång du genomför en aggregering beräkning fönstret innehåller data från föregående fönster sista 1 sekund och inga nya data i nästa 1 sekund.

![Exempel inledande fönster med temperatur händelser](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Exempel fönster med temperatur händelser efter glidande](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

I följande exempel uppdatera vi den kod som använder DummySource att samla in batcharna i ett fönster med en minuts varaktighet och en minut bild.

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

Efter den första minuten detta kommer att ge 12 poster - sex poster från var och en av de två batcharna som samlas in i fönstret.

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

Glidande fönstret tillgängliga funktioner i Spark Streaming API innehåller fönster, countByWindow, reduceByWindow och countByValueAndWindow. Mer information om dessa funktioner finns [omformningar på DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Kontrollpunkter
Om du vill tillhandahålla återhämtning och feltolerans, Spark Streaming förlitar sig på kontrollpunkter till att säkerställa att dataströmmen bearbetning kan fortsätta utan avbrott, även i händelse av nodfel. I HDInsight skapar Spark kontrollpunkter till beständig lagring (Azure Storage eller Azure Data Lake Store). Kontrollpunkterna lagrar metadata om strömning programmet, till exempel konfiguration, de åtgärder som definierats av programmet och eventuella batchar som placerats i kö men ännu inte behandlats. I vissa fall tas även kontrollpunkterna spara data i RDDs att förkorta den tid det tar för att återskapa data från vad som finns i RDDs som hanteras av Spark tillstånd.

## <a name="deploying-spark-streaming-applications"></a>Distribuera Spark Streaming program
Du vanligtvis skapa Spark Streaming programmet lokalt och distribuera den till Spark i HDInsight genom att kopiera JAR-fil som innehåller programmet till standardlagring kopplad till ditt HDInsight-kluster. Du kan starta tillämpningsprogrammet med hjälp av LIVIUS REST-API finns i klustret. Detta är en POST-åtgärd där innehållet i posten innehåller en JSON-dokument som innehåller sökvägen till din JAR, namnet på den klass vars main-metoden definierar och kör programmet strömmande och eventuellt resurskraven för jobbet (till exempel antalet ex ecutors, minne och kärnor), och kräver att alla konfigurationsinställningar för programkoden.

![Exempel fönster med temperatur händelser efter glidande](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Status för alla program kan också kontrolleras med en GET-begäran mot en LIVIUS slutpunkt. Slutligen kan du avsluta ett program som körs genom att utfärda en begäran om borttagning mot LIVE slutpunkten. Mer information om LIVIUS API: et finns [Remote jobb med LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Apache Spark-kluster i HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark Streaming Programmeringsguide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Starta Spark jobb via fjärranslutning med LIVY](apache-spark-livy-rest-interface.md)