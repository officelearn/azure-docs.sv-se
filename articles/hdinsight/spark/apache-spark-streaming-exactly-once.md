---
title: Skapa Spark Streaming jobb med exakt-gång bearbetning - Azure HDInsight
description: Hur du ställer in Spark Streaming för att bearbeta en händelse en gång och bara en gång.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 388723624fde73899809b95ff8ae4ee23cf49a9d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705098"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Skapa Apache Spark Streaming jobb med exakt-gång bearbetning

Stream bearbetning program vidta olika metoder för hur de hanterar igen bearbetnings meddelanden efter några fel i systemet:

* Minst en gång: Varje meddelande som kommer att bearbetas, men det kan bearbetas mer än en gång.
* Högst en gång: Varje meddelande kan eller inte kan bearbetas. Om meddelandet har bearbetats kan bearbetas det bara en gång.
* Exakt en gång: Varje meddelande är säkert att bearbetas en gång och bara en gång.

Den här artikeln visar hur du konfigurerar Spark Streaming för att få exakt-bearbetning av en gång.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Exakt – en semantik med Apache Spark-strömning

Överväg först hur alla system i felpunkter starta om när du har ett problem och hur du kan undvika dataförlust. Ett program med Spark Streaming har:

* Ingen källa.
* En eller flera mottagare processer som hämtar data från Indatakällan.
* Uppgifter som bearbetar data.
* En utdatamottagare.
* En drivrutin process som hanterar långvariga jobb.

Exakt-när semantik kräver att ingen data går förlorad när som helst och den meddelandebehandling är kan startas om, oavsett där felet inträffade.

### <a name="replayable-sources"></a>Replayable källor

Spark Streaming programmet läser händelser från måste vara *replayable*. Det innebär att i fall där meddelandet hämtades men sedan systemet misslyckades innan meddelandet kunde sparas eller bearbetas källa måste du ange samma meddelande igen.

I Azure, både Azure Event Hubs och [Apache Kafka](https://kafka.apache.org/) på HDInsight ger replayable källor. Ett annat exempel på en replayable källa är en feltolerant filsystem som [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), Azure Storage-blobbar eller Azure Data Lake Storage där alla data förblir alltid och när som helst kan du uppdatera läser dessa data i sin helhet.

### <a name="reliable-receivers"></a>Tillförlitlig mottagare

Datakällor i Spark Streaming Event Hubs och Kafka har *tillförlitlig mottagare*, där varje mottagare håller reda på förloppet läsning av källan. En tillförlitlig mottagare kvarstår dess tillstånd till feltolerant lagring, antingen inom [Apache ZooKeeper](https://zookeeper.apache.org/) eller i Spark Streaming kontrollpunkter som skrivs till HDFS. Om en sådan mottagare misslyckas och senare startas om, kan det ta över där den avbröts.

### <a name="use-the-write-ahead-log"></a>Använd Write-Ahead loggen

Spark Streaming stöder användning av en Skriv-Ahead loggen, där varje mottagna händelse först skrivs till Sparks kontrollpunkt katalog i feltolerant storage och sedan sparas i en Resilient Distributed Dataset (RDD). I Azure är feltolerant lagringen HDFS som backas upp av Azure Storage eller Azure Data Lake Storage. I ditt Spark Streaming-program, Skriv-Ahead loggen är aktiverad för alla mottagare genom att ange den `spark.streaming.receiver.writeAheadLog.enable` konfigurationsinställning till `true`. Skriv-Ahead loggen ger feltolerans för fel i både drivrutinen och executors.

Varje RDD är per definition både replikeras och distribueras över flera arbetare för arbeten aktiviteter som körs mot händelsedata. Om en aktivitet misslyckas eftersom den worker som kör den kraschat uppgiften kommer att startas om i en annan arbetsprocess som har en replik av händelsedata, så förloras inte händelsen.

### <a name="use-checkpoints-for-drivers"></a>Använda kontrollpunkter för drivrutiner

Jobbet drivrutinerna måste vara kan startas om. Om drivrutinen programkörning Spark Streaming kraschar tar det med den alla pågående mottagare, uppgifter och eventuella Rdd lagring av händelsedata. I det här fallet behöver för att kunna spara förloppet för jobbet så att du kan återuppta den senare. Detta åstadkoms genom kontrollpunkter i dirigeras acykliska diagram (DAG) av DStream regelbundet till feltolerant lagring. DAG metadata innehåller konfigurationen används för att skapa strömmande programmet, de åtgärder som definierar programmet och eventuella batchar som placerats i kö men ännu inte slutförts. Dessa metadata gör det möjligt för en misslyckad drivrutin startas från kontrollpunkt information. När drivrutinen har startats om startar nya mottagare att själva återställa händelsedata tillbaka till rdd-datauppsättningar från Write-Ahead loggen.

Kontrollpunkter är aktiverade i Spark Streaming i två steg. 

1. Konfigurera lagringssökväg för kontrollpunkterna i StreamingContext-objekt:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    I HDInsight, bör kontrollpunkterna sparas till standardlagring kopplat till ditt kluster, antingen Azure Storage eller Azure Data Lake Storage.

2. Ange sedan en Kontrollpunktsintervall (i sekunder) på DStream. Vid varje intervall beständiga tillståndsdata som härletts från den inkommande händelsen till lagring. Beständiga data kan minska beräkningen behövs när tillstånd från händelsen som källan.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Använd idempotenta mottagare

Destination-mottagare som jobbet skriver resultat måste kunna hantera situationen där den får samma resultat mer än en gång. Mottagaren måste kunna identifiera sådana duplicerade resultat och ignoreras. En *idempotenta* mottagare kan anropas flera gånger med samma data med ingen ändring av tillstånd.

Du kan skapa idempotenta mottagare genom att implementera logik som först kontrollerar om finns på inkommande resultatet i datalager. Om resultatet redan finns visas skrivningen ska lyckas ur ditt Spark-jobb, men i verkligheten ditt datalager ignoreras den duplicerade informationen. Om resultatet inte finns ska sedan mottagaren infoga nya resultatet i dess lagring. 

Du kan till exempel använda en lagrad procedur med Azure SQL Database som infogar händelser i en tabell. Den här lagrade proceduren först letas upp i händelse av nyckelfält, men endast när ingen matchande händelse att hitta posten infogas i tabellen.

Ett annat exempel är att använda ett partitionerade filsystem, som Azure Storage-blobbar eller Azure Data Lake Storage. I det här fallet behöver inte mottagare logik att söka efter förekomsten av en fil. Om filen som representerar händelsen finns skrivs det bara över med samma data. I annat fall skapas en ny fil på den beräknade sökvägen.

## <a name="next-steps"></a>Nästa steg

* [Apache Spark-strömning översikt](apache-spark-streaming-overview.md)
* [Skapa högtillgängliga Apache Spark Streaming jobb i Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
