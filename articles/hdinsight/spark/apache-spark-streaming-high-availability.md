---
title: Skapa högtillgängliga Spark Streaming jobb i YARN - Azure HDInsight
description: Hur du ställer in Spark Streaming för ett scenario med hög tillgänglighet.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: 1d9a7caa7ab70ef1f0da41e1ec3f30780f93536a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60536958"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Skapa hög tillgänglighet Apache Spark Streaming jobb med YARN

[Apache Spark](https://spark.apache.org/) strömning gör det möjligt att implementera skalbara, högt dataflöde, feltoleranta program för bearbetning av dataströmmar. Du kan ansluta Spark Streaming program på ett HDInsight Spark-kluster till en mängd olika datakällor, till exempel Azure Event Hubs, Azure IoT Hub [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ ZeroMQ](http://zeromq.org/), raw TCP sockets, eller genom att övervaka den [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) filsystem för ändringar. Spark Streaming har stöd för feltolerans med garanti att en given händelse bearbetas exakt en gång, även med ett nodfel.

Spark Streaming skapar långvariga jobb som du kan använda omvandlingar för data och skicka sedan resultatet till filsystem, databaser, instrumentpaneler och i konsolen. Spark Streaming bearbetar micro-batchar av data, genom att första samla in en batch med händelser under en definierad tidsintervall. Därefter skickas den batch på för bearbetning och utdata. Batch tidsintervall definieras vanligen i bråkdelar av en sekund.

![Spark-strömning](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark Streaming representerar en ständig ström med data med hjälp av en *diskretiserade stream* (DStream). Den här DStream kan skapas från inmatningskällor som Event Hubs eller Kafka eller genom att använda omformningar på en annan DStream. När en händelse anländer till programmets Spark Streaming, lagras händelsen på ett tillförlitligt sätt. Det vill säga replikeras händelsedata så att flera noder har en kopia av den. Detta säkerställer att fel på någon nod inte resulterar i förlust av din händelse.

Spark core använder *elastiska distribuerade datauppsättningar* (rdd-datauppsättningar). Rdd distribuera data över flera noder i klustret, där varje nod Allmänt underhåller data helt i minnet för bästa prestanda. Varje RDD representerar händelser som samlas in med ett batch-intervall. När det ska gå att batch-intervallet, genererar Spark Streaming en ny RDD som innehåller alla data i det här intervallet. Den här kontinuerlig uppsättning Rdd har samlats in i en DStream. Ett program med Spark Streaming bearbetar data som lagras i varje batch RDD.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark Structured Streaming jobb

Spark Structured Streaming introducerades i Spark 2.0 som en analytisk motor för användning på strömmande strukturerade data. Spark Structured Streaming använder SparkSQL batchbearbetning motorn API: er. Precis som med Spark Streaming, körs Spark Structured Streaming dess beräkningar över kontinuerligt inkommer micro-batchar av data. Spark Structured Streaming representerar en dataström som en indata-tabell med ett obegränsat antal rader. Det vill säga fortsätter tabellen indata att växa när nya data tas emot. Den här indata-tabellen bearbetas kontinuerligt av en tidskrävande fråga och resultatet skrivs till en tabell.

![Apache Spark Structured Streaming](./media/apache-spark-streaming-high-availability/structured-streaming.png)

I Structured Streaming data anländer till systemet och omedelbart matas in i tabellen indata. Du kan skriva frågor som utför åtgärder mot den här indata-tabellen. Frågeresultatet visas ger en annan tabell som heter resultattabellen. Resultattabellen innehåller resultatet av din fråga, från vilken du rita data som skickas till en extern datalager en relationsdatabas. Den *utlösaren intervall* ställer in tiderna för när data har bearbetats från indata-tabellen. Som standard bearbetar Structured Streaming data så fort de anländer. Du kan också konfigurera att utlösaren ska köras på ett längre intervall, så strömmande data bearbetas batchvis tidsbaserade. Data i tabellen resultaten kan vara helt uppdateras varje gång som det finns nya data så att det innehåller alla utdata som sedan strömmande frågan påbörjades (*fullständig läge*), eller det får bara innehålla bara de data som är nytt sedan senast tid som frågan bearbetades (*lägga till*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Skapa feltolerant Spark Streaming jobb

Börja med att koda dina enskilda jobb för återställning i händelse av fel om du vill skapa en miljö med hög tillgänglighet för dina jobb med Spark Streaming. Sådana lokal återställs jobben är feltolerant.

Rdd har flera egenskaper som hjälper med hög tillgänglighet och feltolerant Spark Streaming jobb:

* Antal inkommande data som lagras i rdd-datauppsättningar som en DStream replikeras automatiskt i minnet för feltolerans.
* Data som går förlorade på grund av worker-fel kan vara recomputed från replikerade inkommande data på olika arbetare så länge de arbetsnoderna som är tillgängliga.
* Snabb återställning av fel inträffa inom en sekund när återställningen från fel / ”stragglers” sker via beräkning i minnet.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Exakt – en semantik med Spark Streaming

Om du vill skapa ett program som bearbetar varje händelse en gång (och bara en gång), Överväg hur alla system i felpunkter starta om när du har ett problem och hur du kan undvika dataförlust. Exakt-när semantik kräver att ingen data går förlorad när som helst och den meddelandebehandling är kan startas om, oavsett där felet inträffade. Se [skapa Spark Streaming jobb med exakt-gång bearbetning av](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark-strömning och Apache Hadoop YARN

I HDInsight, kluster work koordinerar *ännu en annan Resource Negotiator* (YARN). Designa hög tillgänglighet för Spark Streaming innehåller metoder för Spark Streaming och även för YARN-komponenter.  Ett exempel på en konfiguration med YARN visas nedan. 

![YARN-arkitektur](./media/apache-spark-streaming-high-availability/yarn-arch.png)

I följande avsnitt beskrivs designöverväganden för den här konfigurationen.

### <a name="plan-for-failures"></a>Planera för fel

Om du vill skapa en YARN-konfiguration för hög tillgänglighet bör du planera för executor eller drivrutinen inte. Vissa Spark Streaming jobb kan även innehålla data garanti krav som behöver ytterligare konfiguration och installation. En strömmande programmet kan till exempel ha ett företagskrav för ett noll--dataförlust garanterar trots eventuella fel som uppstår i värdsystemet för direktuppspelning eller HDInsight-kluster.

Om en **executor** misslyckas, dess uppgifter och mottagare har startats om av Spark automatiskt, så det finns ingen ändring behövs.

Men om en **drivrutinen** misslyckas alla dess associerade executors misslyckas och alla mottagna block och beräkning resultat går förlorade. Om du vill återställa från fel i en drivrutin, använda *DStream kontrollpunkter* enligt beskrivningen i [skapa Spark Streaming jobb med exakt-gång bearbetning av](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). DStream kontrollpunkter sparar regelbundet den *riktad Acyklisk graf* (DAG) av DStreams till feltolerant lagring till exempel Azure Storage.  Kontrollpunkter kan Spark Structured Streaming att starta om misslyckade drivrutinen från kontrollpunkt information.  Drivrutinen omstarten startar nya executors och startar också om mottagare.

För att återställa drivrutiner med DStream kontrollpunkter:

* Konfigurera automatisk drivrutin omstart på YARN med Konfigurationsinställningen `yarn.resourcemanager.am.max-attempts`.
* Ange en kontrollpunkt katalog i ett HDFS-kompatibla filsystem med `streamingContext.checkpoint(hdfsDirectory)`.
* Omstrukturera källkoden för att använda kontrollpunkter för återställning, till exempel:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Konfigurera återställning av förlorade data genom att aktivera skrivning-ahead loggen (WAL) med `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`, och inaktivera replikering i minnet för inkommande DStreams med `StorageLevel.MEMORY_AND_DISK_SER`.

Sammanfattningsvis med hjälp av kontrollpunkter, WAL + tillförlitlig mottagare, du kommer att kunna leverera ”minst en gång” återställning av data:

* Exakt när, så länge mottagna data förloras inte och utdata är antingen idempotenta eller Transaktionsreplikering.
* Exakt en gång med den nya Kafka direkt metoden som använder Kafka som en replikerad logg, snarare än att använda mottagare eller WALs.

### <a name="typical-concerns-for-high-availability"></a>Vanliga frågor för hög tillgänglighet

* Det är svårare att övervaka direktuppspelningsjobb än batch-jobb. Spark Streaming jobben är vanligtvis tidskrävande och YARN sammanställa inte loggar tills ett jobb har slutförts.  Spark kontrollpunkter förloras under program- eller Spark-uppgraderingar och du måste ta bort kontrollpunkt-katalogen under en uppgradering.

* Konfigurera din YARN klusterläge för att köra drivrutiner, även om en klient inte. Du ställer in automatisk omstart för drivrutiner:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark och Spark Streaming-Gränssnittet har ett konfigurerbart mått. Du kan också använda ytterligare bibliotek, till exempel grafit/Grafana för att ladda ned instrumentpanelen som num poster bearbetas, ' Minne/GC användning på drivrutinen & executors', 'total fördröjning', 'användning av klustret' och så vidare. Du kan använda i Structured Streaming version 2.1 eller större, `StreamingQueryListener` att samla in ytterligare mått.

* Du bör ändra långvariga jobb.  När ett program med Spark Streaming skickas till klustret måste ha definierats YARN kön där jobbet körs. Du kan använda en [YARN kapacitet Scheduler](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) skicka långvariga jobb för att avgränsa köer.

* Stänga av programmets strömmande ett smidigt sätt. Om din förskjutningar är kända och alla programtillstånd lagras externt, kan du programmässigt stoppa strömning programmet på motsvarande ställe. En metod är att använda ”tråd hookar” i Spark sker genom att markera en extern flaggan varje *n* sekunder. Du kan också använda en *markörfil* som har skapats på HDFS när programmet startas och sedan tas bort när du vill stoppa. För en markör filen metod, använder du en separat tråd i ditt Spark-program som anropar koden ungefär så här:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Nästa steg

* [Apache Spark-strömning översikt](apache-spark-streaming-overview.md)
* [Skapa Apache Spark Streaming jobb med exakt-gång bearbetning](apache-spark-streaming-exactly-once.md)
* [Långvariga Apache Spark-Direktuppspelningsjobb på YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Strukturerad direktuppspelning: Fel feltoleranta semantik](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diskretiserade strömmar: En feltolerant modell för skalbar Stream bearbetning](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
