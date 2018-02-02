---
title: "Skapa hög tillgänglighet Spark Streaming jobb i YARN - Azure HDInsight | Microsoft Docs"
description: "Hur du ställer in Spark Streaming för ett scenario med hög tillgänglighet."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: f916f9939ac9683a2ee162ba4d2105f66187b111
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="create-high-availability-spark-streaming-jobs-with-yarn"></a>Skapa hög tillgänglighet Spark Streaming jobb med YARN

Spark Streaming kan du implementera skalbara, hög genomströmning feltolerant program för bearbetning av dataströmmar. Du kan ansluta Spark Streaming program på ett HDInsight Spark-kluster till en mängd olika datakällor, till exempel Azure Event Hubs, Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ, rena TCP-sockets eller genom att övervaka HDFS-filsystem för ändringar. Spark Streaming stöder feltolerans med säkerhet att en given händelse bearbetas exakt en gång, även om ett nodfel.

Spark Streaming skapar tidskrävande jobb under vilken du kan använda omformningar på data och skicka sedan resultatet till filsystem, databaser, instrumentpaneler och konsolen. Spark Streaming bearbetar micro batchar av data genom att först samla in en batch med händelser under en definierad tidsintervall. Därefter skickas detta batch på för bearbetning och utdata. Batch tidsintervall definieras vanligen i delar av en sekund.

![Spark Streaming](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark Streaming representerar en ständig ström med data med hjälp av en *diskretiserade dataströmmen* (DStream). Den här DStream kan skapas från indatakällor som Händelsehubbar eller Kafka, eller genom att använda omformningar på en annan DStream. När en händelse når programmet Spark Streaming, lagras händelsen på ett tillförlitligt sätt. Händelsedata som replikeras som är så att flera noder har en kopia av den. Detta säkerställer att fel på en enskild nod inte resulterar i förlust av din händelse.

Spark core använder *flexibel distribuerade datauppsättningar* (RDDs). RDDs fördela data över flera noder i klustret, där varje nod vanligtvis underhåller data helt i minnet för bästa prestanda. Varje RDD representerar händelser som samlas in via ett batch-intervall. När batch-intervall långa genererar Spark Streaming en ny RDD som innehåller alla data i intervallet. Den här kontinuerlig uppsättning RDDs har samlats in i en DStream. Ett program med Spark Streaming bearbetar data som lagras i varje batch RDD.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark Streaming strukturerade jobb

Spark Streaming strukturerade introducerades i Spark 2.0 som en analytiska motor för direktuppspelning strukturerade data. Spark Streaming strukturerade använder SparkSQL batchbearbetning motorns API: er. Precis som med Spark Streaming körs Spark strukturerade strömning dess beräkningar över kontinuerligt anländer micro-batchar av data. Spark Streaming strukturerade representerar en dataström som ett indata-tabell med obegränsad rader. Det vill säga fortsätter tabellen indata att växa när nya data tas emot. Indata tabellen bearbetas kontinuerligt av en tidskrävande fråga och resultatet skrivs till en tabell.

![Spark strukturerad strömning](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Med strukturerade strömmande data når systemet och omedelbart inhämtas i Input-tabellen. Du kan skriva frågor som utför åtgärder mot den här tabellen indata. Frågeresultatet ger en annan tabell som kallas resultattabellen. Tabellen resultat innehåller resultatet av din fråga, varifrån du ritar data som ska skickas till en extern datalagret en relationsdatabas. Den *utlösaren intervall* anger tiden för när data bearbetas från tabellen indata. Som standard bearbetar strukturerade strömning data när den tas emot. Du kan också konfigurera utlösaren ska köras på ett längre intervall, så strömmande data bearbetas i tidsbaserade grupper. Data i tabellen resultaten kan vara helt uppdateras varje gång det finns nya data så att det innehåller alla utdata sedan strömmande frågan påbörjades (*fullständig läge*), eller endast innehåller bara de data som är nytt sedan senast tid som frågan bearbetades (*lägga till*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Skapa feltoleranta Spark Streaming jobb

Börja med att koda dina enskilda jobb för återställning vid fel för att skapa en miljö med hög tillgänglighet för ditt Spark Streaming jobb. Sådana själva återställs jobb är feltolerant.

RDDs har flera egenskaper som hjälper hög tillgänglighet och feltolerant Spark Streaming jobb:

* Antal inkommande data som lagras i RDDs som en DStream replikeras automatiskt i minnet för feltolerans.
* Data som går förlorade på grund av worker-fel kan vara recomputed från replikerade inkommande data på olika arbetare så länge de arbetsnoderna som är tillgängliga.
* Snabb återställning av fel kan uppstå i en sekund som återställning efter fel / ”stragglers” sker via beräkning i minnet.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Exakt-semantik en gång med Spark Streaming

Om du vill skapa ett program som bearbetar varje händelse en gång (och bara en gång), Överväg hur alla system i felpunkter startas om efter att ha ett problem och hur du kan undvika dataförlust. Exakt-när semantik kräver att ingen data går förlorad när som helst och som har bearbetats kan startas om, oavsett var felet inträffar. Se [skapa Spark Streaming jobb med exakt-när händelsen bearbetning](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-yarn"></a>Spark Streaming och YARN

I HDInsight, kluster arbete koordinerar *ännu en annan resurs förhandlare* (YARN). Designa hög tillgänglighet för Spark Streaming innehåller metoder för Spark Streaming och för YARN-komponenter.  Ett exempel på en konfiguration med hjälp av YARN visas nedan. 

![YARN-arkitektur](./media/apache-spark-streaming-high-availability/yarn-arch.png)

I följande avsnitt beskrivs överväganden vid utformning för den här konfigurationen.

### <a name="plan-for-failures"></a>Planera för fel

Om du vill skapa en YARN-konfiguration för hög tillgänglighet bör du planera för utföraren eller drivrutinen inte. Vissa Spark Streaming jobb också innefatta krav för säkerhet som kräver ytterligare konfiguration och installation. Strömmande program kan exempelvis ha en affärsbehoven för ett noll--dataförlust garantera trots eventuella fel som uppstår i värdsystemet strömmande eller HDInsight-kluster.

Om en **utföraren** misslyckas med uppgifter och mottagare har startats om av Spark automatiskt, så det finns ingen konfigurationsändring som behövs.

Men om en **drivrutinen** misslyckas, kommer alla dess associerade executors misslyckas och försvinner alla mottagna block och beräkning resultat. Om du vill återställa från fel i en drivrutin, Använd *DStream kontrollpunkter* enligt beskrivningen i [skapa Spark Streaming jobb med exakt-när händelsen bearbetning](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). DStream kontrollpunkter sparar regelbundet den *dirigeras acykliskt diagram* (DAG) av DStreams till feltolerant lagring såsom Azure Storage.  Kontrollpunkter kan Spark strukturerade strömning att starta om misslyckade drivrutinen från kontrollpunkt information.  Drivrutinen omstarten startar nya executors och startar också om mottagare.

För att återställa drivrutiner med DStream kontrollpunkter:

* Konfigurera automatisk drivrutin omstart på YARN med Konfigurationsinställningen `yarn.resourcemanager.am.max-attempts`.
* Ange en kontrollpunkt katalog i ett filsystem i HDFS-kompatibla med `streamingContext.checkpoint(hdfsDirectory)`.
* Omstrukturera källkod för att använda kontrollpunkter för återställning, till exempel:

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

* Konfigurera förlorade data genom att aktivera write-ahead-loggen (förnyelse) `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`, och inaktivera InMemory-replikering för inkommande DStreams med `StorageLevel.MEMORY_AND_DISK_SER`.

Sammanfattningsvis med kontrollpunkter, förnyelse + tillförlitliga mottagare kan du kommer att kunna leverera ”minst en gång” återställning av data:

* Exakt när, så länge mottagna data är inte förlorade och utdata är antingen idempotent eller transaktionsmeddelanden.
* Exakt en gång med den nya Kafka direkt metoden som använder Kafka som en replikerad logg i stället för att använda mottagare eller WALs.

### <a name="typical-concerns-for-high-availability"></a>Vanliga frågor för hög tillgänglighet

* Det är svårare att övervaka strömmande jobb än batchjobb. Spark Streaming jobb är vanligtvis långvariga och YARN sammanställa inte loggar tills ett jobb har slutförts.  Spark kontrollpunkter förloras under program- eller Spark uppgraderingar och du behöver ta bort kontrollpunkten-katalogen under en uppgradering.

* Konfigurera YARN kluster-läge för att köra drivrutiner, även om en klient inte. Att ställa in automatisk omstart efter drivrutiner:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark och Spark Streaming Gränssnittet har ett konfigurerbart mått. Du kan också använda ytterligare bibliotek, till exempel grafit/Grafana för att hämta instrumentpanelen mått som num poster bearbetas, ' Minne/GC användning på drivrutinen & executors', 'total fördröjning', 'användning av klustret' och så vidare. I strukturerade direktuppspelning version 2.1 eller senare, kan du använda `StreamingQueryListener` att samla in ytterligare mått.

* Du bör segmentera tidskrävande jobb.  När ett program med Spark Streaming skickas till klustret måste ha definierats YARN kön där jobbet körs. Du kan använda en [YARN kapacitet Scheduler](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) skicka tidskrävande jobb till separata köer.

* Stäng strömmande programmet avslutas. Om din förskjutningar är kända och alla programtillstånd lagras externt, kan du programmässigt stoppa strömning programmet på rätt plats. En metod är att använda ”tråd hook” i Spark, genom att kontrollera en extern flaggan varje  *n*  sekunder. Du kan också använda en *markör filen* som har skapats på HDFS när programmet startas och sedan tas bort när du vill avbryta. Använd en separat tråd för en markör filen metod i ditt Spark-program som anropar koden ut ungefär så här:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Nästa steg

* [Spark Streaming: översikt](apache-spark-streaming-overview.md)
* [Skapa Spark Streaming jobb med exakt-när händelsen bearbetning](apache-spark-streaming-exactly-once.md)
* [Långvariga Spark Streaming jobb på YARN](http://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Strukturerade strömning: Fel feltoleranta semantik](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diskretiserade dataströmmar: En feltolerant modell för skalbara dataströmmen bearbetning](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
