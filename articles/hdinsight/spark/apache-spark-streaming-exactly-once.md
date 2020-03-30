---
title: Spark Streaming & exakt en gång händelsebearbetning - Azure HDInsight
description: Så här konfigurerar du Apache Spark Streaming för att bearbeta en händelse en gång och bara en gång.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2018
ms.openlocfilehash: ee4f9b84e822cb370e5fe3d55fcceb9c8a9f2ab9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228975"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Skapa Apache Spark Streaming-jobb med exakt en gång händelsebearbetning

Stream bearbetning program ta olika metoder för hur de hanterar upparbetning meddelanden efter några fel i systemet:

* Minst en gång: Varje meddelande är garanterat att bearbetas, men det kan få bearbetas mer än en gång.
* Högst en gång: Varje meddelande kanske eller kanske inte bearbetas. Om ett meddelande bearbetas bearbetas det bara en gång.
* Exakt en gång: Varje meddelande är garanterat att behandlas en gång och bara en gång.

Den här artikeln visar hur du konfigurerar Spark Streaming för att uppnå exakt en gång bearbetning.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Exakt en gång semantik med Apache Spark Streaming

Tänk först på hur alla systempunkter för fel startar om efter att ha ett problem och hur du kan undvika dataförlust. Ett Spark Streaming-program har:

* En ingångskälla.
* En eller flera mottagare bearbetar data från indatakällan.
* Uppgifter som bearbetar data.
* En utdatamottagare.
* En drivrutinsprocess som hanterar det långvariga jobbet.

Exakt en gång-semantik kräver att inga data går förlorade vid något tillfälle, och att meddelandebearbetningen kan startas om, oavsett var felet inträffar.

### <a name="replayable-sources"></a>Källor som kan spelas om

Källan som Spark Streaming-programmet läser dina händelser från måste kunna *spelas upp*igen . Det innebär att i de fall där meddelandet hämtades men sedan systemet misslyckades innan meddelandet kunde sparas eller bearbetas, måste källan ange samma meddelande igen.

I Azure tillhandahåller både Azure Event Hubs och [Apache Kafka](https://kafka.apache.org/) på HDInsight reningsbara källor. Ett annat exempel på en återspelbar källa är ett feltolerant filsystem som [Apache Hadoop HDFS,](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)Azure Storage blobbar eller Azure Data Lake Storage, där alla data sparas för alltid och när som helst kan du läsa om data i sin helhet.

### <a name="reliable-receivers"></a>Pålitliga mottagare

I Spark Streaming har källor som Event Hubs och Kafka *tillförlitliga mottagare*, där varje mottagare håller reda på dess framsteg som läser källan. En tillförlitlig mottagare behåller sitt tillstånd i feltolerant lagring, antingen inom [Apache ZooKeeper](https://zookeeper.apache.org/) eller i Spark Streaming-kontrollpunkter skrivna till HDFS. Om en sådan mottagare misslyckas och senare startas om, kan den fortsätta där den slutade.

### <a name="use-the-write-ahead-log"></a>Använda write-ahead-loggen

Spark Streaming stöder användningen av en write-ahead log, där varje mottagen händelse först skrivs till Spark's checkpoint katalog i feltolerant lagring och sedan lagras i en flexibel distribuerad datauppsättning (RDD). I Azure är den feltoleranta lagringen HDFS som backas upp av antingen Azure Storage eller Azure Data Lake Storage. I ditt Spark Streaming-program är write-ahead-loggen aktiverad `spark.streaming.receiver.writeAheadLog.enable` för `true`alla mottagare genom att ställa in konfigurationsinställningen på . Write-Ahead Log ger feltolerans för fel på både drivrutinen och utförarna.

För arbetare som kör uppgifter mot händelsedata är varje RDD per definition både replikerad och distribuerad över flera arbetare. Om en aktivitet misslyckas eftersom arbetaren som kör den kraschade, startas aktiviteten om på en annan arbetare som har en replik av händelsedata, så händelsen går inte förlorad.

### <a name="use-checkpoints-for-drivers"></a>Använda kontrollpunkter för drivrutiner

Jobbdrivrutinerna måste kunna startas om. Om drivrutinen som kör ditt Spark Streaming-program kraschar tar den ner med allt som körs mottagare, uppgifter och alla RDD:er som lagrar händelsedata. I det här fallet måste du kunna spara förloppet för jobbet så att du kan återuppta det senare. Detta åstadkoms genom att kontrollpunkten för DStream (Directed Acyclic Graph) i DStream regelbundet till feltolerant lagring. DAG-metadata innehåller den konfiguration som används för att skapa direktuppspelningsprogrammet, de åtgärder som definierar programmet och alla batchar som är köade men ännu inte slutförda. Med den här metadata kan en misslyckad drivrutin startas om från kontrollpunktsinformationen. När drivrutinen startas om startar nya mottagare som själva återställer händelsedata tillbaka till RDD:er från write-ahead-loggen.

Kontrollpunkter är aktiverade i Spark Streaming i två steg.

1. Konfigurera lagringssökvägen för kontrollpunkterna i StreamingContext-objektet:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    I HDInsight bör dessa kontrollpunkter sparas i standardlagringen som är kopplad till klustret, antingen Azure Storage eller Azure Data Lake Storage.

2. Ange sedan ett kontrollpunktsintervall (i sekunder) på DStream. Vid varje intervall sparas tillståndsdata som härleds från indatahändelsen till lagring. Beständiga tillståndsdata kan minska den beräkning som behövs när tillståndet återskapas från källhändelsen.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Använd idempotenta sänkor

Målet sjunker som ditt jobb skriver resultat måste kunna hantera situationen där det ges samma resultat mer än en gång. Diskbänken måste kunna upptäcka sådana dubbla resultat och ignorera dem. En *idempotent* diskho kan anropas flera gånger med samma data utan ändring av tillstånd.

Du kan skapa idempotenta sänkor genom att implementera logik som först kontrollerar förekomsten av det inkommande resultatet i datalagret. Om resultatet redan finns, ska skrivningen visas för att lyckas ur spark-jobbet, men i verkligheten ignorerade datalagringen dubblettdata. Om resultatet inte finns, bör diskhon infoga det nya resultatet i lagringen.

Du kan till exempel använda en lagrad procedur med Azure SQL Database som infogar händelser i en tabell. Den här lagrade proceduren slår först upp händelsen efter nyckelfält och endast när ingen matchande händelse hittades infogas posten i tabellen.

Ett annat exempel är att använda ett partitionerat filsystem, till exempel Azure Storage-blobbar eller Azure Data Lake Storage. I det här fallet behöver din sink-logik inte kontrollera om det finns en fil. Om filen som representerar händelsen finns skrivs den helt enkelt över med samma data. Annars skapas en ny fil på den beräknade sökvägen.

## <a name="next-steps"></a>Nästa steg

* [Apache Spark Streaming Översikt](apache-spark-streaming-overview.md)
* [Skapa högtillgänge Apache Spark Streaming jobb i Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
