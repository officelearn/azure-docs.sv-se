---
title: Spark Streaming-jobb med högtillgänge i YARN - Azure HDInsight
description: Konfigurera Apache Spark Streaming för ett scenario med hög tillgänglighet i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: ac51b77e1ffc2b476b0a73dac9b6917552a86ce4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807161"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Skapa apache gn streaming-jobb med hög tillgänglighet med YARN

[Apache gnista](https://spark.apache.org/) Med direktuppspelning kan du implementera skalbara, höggenomströmningsbara, feltoleranta program för bearbetning av dataströmmar. Du kan ansluta Spark Streaming-program i ett HDInsight Spark-kluster till olika typer av datakällor, till exempel Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), raw TCP-sockets eller genom att övervaka [Apache Hadoop HDFS-filsystemet](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) för ändringar. Spark Streaming stöder feltolerans med garantin att en viss händelse bearbetas exakt en gång, även med ett nodfel.

Spark Streaming skapar långvariga jobb under vilka du kan tillämpa omvandlingar på data och sedan skicka ut resultaten till filsystem, databaser, instrumentpaneler och konsolen. Spark Streaming bearbetar mikrobatchar med data genom att först samla in en grupp händelser över ett definierat tidsintervall. Därefter skickas den batchen på för bearbetning och utdata. Batchtidsintervall definieras vanligtvis i bråkdelar av en sekund.

![Gnistströmning](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreams (DStreams)

Spark Streaming representerar en kontinuerlig dataström med hjälp av en *diskret ström* (DStream). Den här DStream kan skapas från indatakällor som Event Hubs eller Kafka, eller genom att tillämpa omvandlingar på en annan DStream. När en händelse anländer till ditt Spark Streaming-program lagras händelsen på ett tillförlitligt sätt. Det vill säga händelsedata replikeras så att flera noder har en kopia av den. Detta säkerställer att felet för en enskild nod inte leder till förlust av din händelse.

Spark-kärnan använder *fjädriga distribuerade datauppsättningar* (RDDs). RDD:er distribuerar data över flera noder i klustret, där varje nod i allmänhet underhåller sina data helt i minnet för bästa prestanda. Varje RDD representerar händelser som samlats in under ett batchintervall. När batchintervallet förflutet producerar Spark Streaming en ny RDD som innehåller alla data i det intervallet. Den här kontinuerliga uppsättningen RDD:er samlas in i en DStream. Ett Spark Streaming-program bearbetar data som lagras i varje batch RDD.

![Spark DStream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark Structured Streaming jobb

Spark Structured Streaming introducerades i Spark 2.0 som en analytisk motor för användning på strömmande strukturerad data. Spark Structured Streaming använder SparkSQL-batchmotor-API:erna. Precis som med Spark Streaming kör Spark Structured Streaming sina beräkningar över kontinuerligt ankommande mikrobatchar med data. Spark Structured Streaming representerar en dataström som en indatatabell med obegränsade rader. Det vill än, indatatabellen fortsätter att växa när nya data anländer. Den här indatatabellen bearbetas kontinuerligt av en tidskrävande fråga och resultaten skrivs ut till en utdatatabell.

![Gnista strukturerad streaming](./media/apache-spark-streaming-high-availability/structured-streaming.png)

I Strukturerad direktuppspelning kommer data till systemet och matas omedelbart in i indatatabellen. Du skriver frågor som utför åtgärder mot den här indatatabellen. Frågeutdata ger en annan tabell, kallad resultattabellen. Resultattabellen innehåller resultatet av frågan, som du hämtar data från till ett externt datalager en sådan relationsdatabas. *Utlösningsintervallet* anger tidpunkten för när data bearbetas från indatatabellen. Som standard bearbetar strukturerad direktuppspelning data så fort de anländer. Du kan dock också konfigurera utlösaren så att den körs med ett längre intervall, så att strömmande data bearbetas i tidsbaserade batchar. Data i resultattabellen kan uppdateras varje gång det finns nya data så att de innehåller alla utdata sedan strömningsfrågan började (*fullständigt läge),* eller så innehåller den bara de data som är nya sedan den senaste gången frågan bearbetades *(bifogat läge*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Skapa feltoleranta Spark Streaming-jobb

Om du vill skapa en miljö med hög tillgänglig tillgång för dina Spark Streaming-jobb börjar du med att koda dina enskilda jobb för återställning i händelse av fel. Sådana självåterkrävande jobb är feltoleranta.

RDD:er har flera egenskaper som hjälper högtillgänge och feltoleranta Spark Streaming-jobb:

* Batchar med indata som lagras i RDD:er som en DStream replikeras automatiskt i minnet för feltolerans.
* Data som förlorats på grund av arbetarfel kan beräknas om från replikerade indata på olika arbetare, så länge dessa arbetsnoder är tillgängliga.
* Snabb felåterställning kan ske inom en sekund, eftersom återställning från fel/eftersläntrare sker via beräkning i minnet.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Exakt en gång semantik med Spark Streaming

Om du vill skapa ett program som bearbetar varje händelse en gång (och bara en gång) bör du överväga hur alla systempunkter för fel startas om efter att ha ett problem och hur du kan undvika dataförlust. Exakt när semantik kräver att inga data går förlorade vid något tillfälle, och att meddelandebearbetningen kan startas om, oavsett var felet inträffar. Se [Skapa Spark Streaming-jobb med exakt en gång händelsebearbetning](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark Streaming och Apache Hadoop YARN

I HDInsight samordnas klusterarbete av *Ännu en resursförhandlare* (YARN). Designa hög tillgänglighet för Spark Streaming innehåller tekniker för Spark Streaming, och även för YARN-komponenter.  En exempelkonfiguration med YARN visas nedan.

![YARN Arkitektur](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

I följande avsnitt beskrivs designöverväganden för den här konfigurationen.

### <a name="plan-for-failures"></a>Planera för fel

Om du vill skapa en YARN-konfiguration för hög tillgänglighet bör du planera för ett eventuellt körnings- eller drivrutinsfel. Vissa Spark Streaming-jobb innehåller också datagarantikrav som behöver ytterligare konfiguration och installation. Ett direktuppspelningsprogram kan till exempel ha ett affärskrav för en nolldataförlustgaranti trots eventuella fel som uppstår i det strömmande systemet eller HDInsight-klustret.

Om en **utförare** misslyckas startas dess uppgifter och mottagare om av Spark automatiskt, så det behövs ingen konfigurationsändring.

Men om en **drivrutin** misslyckas misslyckas alla tillhörande utförare och alla mottagna block och beräkningsresultat går förlorade. Om du vill återställa från ett drivrutinsfel använder du *DStream-kontrollpunkter* som beskrivs i [Skapa Spark Streaming-jobb med exakt en gång händelsebearbetning](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). DStream-kontrollpunkter sparar regelbundet det *riktade acykliska diagrammet* (DAG) för DStreams till feltolerant lagring, till exempel Azure Storage.  Kontrollpunkter gör det möjligt för Spark Structured Streaming att starta om den misslyckade drivrutinen från kontrollpunktsinformationen.  Den här drivrutinen startar nya executors och startar även om mottagare.

Så här återställer du drivrutiner med DStream-kontrollpunkter:

* Konfigurera automatisk omstart av drivrutinen på YARN med konfigurationsinställningen `yarn.resourcemanager.am.max-attempts`.
* Ange en kontrollpunktskatalog i ett `streamingContext.checkpoint(hdfsDirectory)`HDFS-kompatibelt filsystem med .
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

* Konfigurera förlorad dataåterställning genom att aktivera write-ahead log (WAL) med `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`och `StorageLevel.MEMORY_AND_DISK_SER`inaktivera in-memory replication för indata-DStreams med .

Sammanfattningsvis, med hjälp av kontrollpunkter + WAL + pålitliga mottagare, kommer du att kunna leverera "minst en gång" dataåterställning:

* Exakt en gång, så länge mottagna data inte går förlorade och utdata är antingen idempotenta eller transaktionella.
* Exakt en gång, med den nya Kafka Direct-metoden, som använder Kafka som en replikerad logg, i stället för att använda mottagare eller WALs.

### <a name="typical-concerns-for-high-availability"></a>Typiska problem för hög tillgänglighet

* Det är svårare att övervaka streamingjobb än batchjobb. Spark Streaming-jobb körs vanligtvis och YARN sammanställer inte loggar förrän ett jobb är klart.  Spark-kontrollpunkter går förlorade under uppgraderingar av program eller Spark, och du måste rensa kontrollpunktskatalogen under en uppgradering.

* Konfigurera ditt YARN-klusterläge för att köra drivrutiner även om en klient misslyckas. Så här konfigurerar du automatisk omstart för drivrutiner:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark och Gnistströmningsgränssnittet har ett konfigurerbart måttsystem. Du kan också använda ytterligare bibliotek, till exempel Grafit/Grafana för att hämta instrumentpanelsmått som "num records processed", "memory/GC-användning på drivrutinen & executors", "total fördröjning", "användning av klustret" och så vidare. I Strukturerad direktuppspelning version 2.1 `StreamingQueryListener` eller senare kan du använda för att samla in ytterligare mått.

* Du bör segmentera långvariga jobb.  När ett Spark Streaming-program skickas till klustret måste YARN-kön där jobbet körs definieras. Du kan använda en [YARN-kapacitetsschemaläggare](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) för att skicka tidskrävande jobb till separata köer.

* Stäng av ditt streamingprogram på ett smidigt sätt. Om dina förskjutningar är kända och alla programtillstånd lagras externt kan du programmässigt stoppa ditt direktuppspelningsprogram på rätt plats. En teknik är att använda "gängkrokar" i Spark, genom att kontrollera en extern flagga *var n* sekunder. Du kan också använda en *markörfil* som skapas på HDFS när du startar programmet och sedan tas bort när du vill stoppa. För en markörfilmetod använder du en separat tråd i Spark-programmet som anropar kod som liknar detta:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Nästa steg

* [Apache Spark Streaming Översikt](apache-spark-streaming-overview.md)
* [Skapa Apache Spark Streaming-jobb med exakt en gång händelsebearbetning](apache-spark-streaming-exactly-once.md)
* [Långvariga Apache Spark Streaming Jobb på YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Strukturerad streaming: Feltolerant semantik](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diskreta strömmar: En feltolerant modell för skalbar strömbearbetning](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
