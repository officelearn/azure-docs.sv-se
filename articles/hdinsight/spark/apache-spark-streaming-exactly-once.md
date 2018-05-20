---
title: Skapa Spark Streaming jobb med exakt-när händelsen bearbetning - Azure HDInsight | Microsoft Docs
description: Hur du ställer in Spark Streaming att bearbeta en händelse en gång och bara en gång.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: 48b1d3811f3a8f6190c58e9646ab0d820859fc21
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="create-spark-streaming-jobs-with-exactly-once-event-processing"></a>Skapa Spark Streaming jobb med exakt-när händelsen bearbetning

Program för bearbetning av dataströmmen vidta olika strategier hur de hantera nytt bearbetnings meddelanden efter några fel i systemet:

* Minst en gång: varje meddelande garanterat bearbetas, men den kan bearbetas mer än en gång.
* Högst en gång: varje meddelande kanske eller kanske inte kan bearbetas. Om meddelandet har bearbetats kan endast bearbetas en gång.
* Exakt en gång: varje meddelande garanterat bearbetas en gång och bara en gång.

Den här artikeln visar hur du konfigurerar Spark Streaming att uppnå exakt-bearbetning av en gång.

## <a name="exactly-once-semantics-with-spark-streaming"></a>Exakt-semantik en gång med Spark Streaming

Först hur alla system i felpunkter startas om efter att ha ett problem och hur du kan undvika dataförlust. Ett program med Spark Streaming har:

* Ingen källa
* En eller flera mottagare processer som hämtar data från Indatakällan
* Uppgifter som bearbetar data
* Utdatamottagare
* En drivrutin process som hanterar långvariga jobbet

Exakt-när semantik kräver att ingen data går förlorad när som helst och som har bearbetats kan startas om, oavsett var felet inträffar.

### <a name="replayable-sources"></a>Replayable källor

Spark Streaming programmet läser händelserna från källan måste vara *replayable*. Det innebär att i fall där meddelandet har hämtats men sedan systemet misslyckades innan meddelandet kunde beständiga eller bearbetas källan måste ange samma meddelande igen.

Ange replayable källor i Azure, både Azure Event Hubs och Kafka på HDInsight. Ett annat exempel på en replayable källa är en feltolerant filsystem som HDFS, Azure Storage-blobbar eller Azure Data Lake Store, där alla data behålls alltid och när som helst du igen kan läsa data i sin helhet.

### <a name="reliable-receivers"></a>Tillförlitliga mottagare

Datakällor som Händelsehubbar och Kafka har i Spark Streaming *tillförlitliga mottagare*, där varje mottagare håller reda på förloppet läsning källan. En tillförlitlig mottagare kvarstår dess tillstånd till feltolerant lagring i ZooKeeper eller i Spark Streaming kontrollpunkter som skrivs till HDFS. Om en sådan mottagare misslyckas och senare startas om den kan hämta där den avbröts.

### <a name="use-the-write-ahead-log"></a>Använd skrivåtgärd i förväg loggen

Spark Streaming stöder användning av en Skriv-Ahead loggen, där varje mottagna händelsen är först skrivs till Sparks kontrollpunkt katalog i feltolerant lagring och lagras i en flexibel distribuerade datauppsättningen (RDD). I Azure är feltolerant lagringen HDFS backas upp av Azure Storage eller Azure Data Lake Store. I tillämpningsprogrammet Spark Streaming Write-Ahead loggen är aktiverad för alla mottagare genom att ange den `spark.streaming.receiver.writeAheadLog.enable` konfigurationen `true`. Skriv-Ahead loggen feltoleransen för både drivrutinen och executors.

För arbetare pågående aktiviteter mot informationen om händelsen, är varje RDD per definition både replikeras och fördelade på flera arbetare. Om en aktivitet inte eftersom personen som kör den kraschat uppgiften kommer att startas om på en annan worker som har en replik av informationen om händelsen, så händelsen är inte förlorade.

### <a name="use-checkpoints-for-drivers"></a>Använda kontrollpunkter för drivrutiner

Jobbet drivrutinerna måste vara kan startas om. Om drivrutinen kör Spark Streaming programmet kraschar tar med alla körs mottagare, uppgifter och eventuella RDDs lagring av händelsedata. I det här fallet behöver för att kunna spara förloppet för jobbet så att du kan återuppta den senare. Detta kan åstadkommas med kontrollpunkter i dirigeras acykliska diagram (DAG) av DStream med jämna mellanrum för feltolerant lagring. DAG-metadata innehåller konfigurationen används för att skapa programmet strömmande, åtgärder som definierar programmet och eventuella batchar som placerats i kö men ännu inte slutförts. Dessa metadata gör det möjligt för en misslyckad drivrutin startas från kontrollpunkt information. När drivrutinen startas om startar nya mottagare själva återställa informationen om händelsen i RDDs från Write-Ahead-loggen igen.

Kontrollpunkter är aktiverade i Spark Streaming i två steg. 

1. Konfigurera lagringssökvägen för kontrollpunkterna i StreamingContext-objektet:

    val ssc = ny StreamingContext (spark, Seconds(1)) ssc.checkpoint("/path/to/checkpoints")

    I HDInsight, bör kontrollpunkterna sparas standardlagring kopplad till ditt kluster Azure Storage eller Azure Data Lake Store.

2. Ange sedan en Kontrollpunktsintervall (i sekunder) på DStream. Vid varje intervall beständiga tillståndsdata som härletts från indatahändelsen till lagring. Beständiga data kan minska beräkning behövs när tillstånd från källa händelse.

    val rader = ssc.socketTextStream (”värdnamnet”, 9999) lines.checkpoint(30) ssc.start() ssc.awaitTermination()

### <a name="use-idempotent-sinks"></a>Använd idempotent handfat

Mål-mottagare som jobbet skriver resultat måste kunna hantera situation där det anges detta mer än en gång. Sink måste kunna identifiera sådana dubbla resultat och ignoreras. En *idempotent* mottagare kan anropas flera gånger med samma data med ingen ändring av tillstånd.

Du kan skapa idempotent sänkor genom att implementera logik som först kontrollerar förekomsten av inkommande resultatet i datalagret. Om resultatet redan skrivningen ska visas ska lyckas ur ett Spark-jobb, men i verkligheten datalager ignoreras den duplicerade informationen. Om resultatet inte finns ska sedan sink infoga resultatet för den här nya i lagringsplatsen. 

Du kan till exempel använda en lagrad procedur med Azure SQL Database som infogar händelser i en tabell. Den här lagrade proceduren först letar upp händelsen av nyckelfält och endast när ingen matchande händelsen hitta posten infogas i tabellen.

Ett annat exempel är att använda en partitionerad filsystem, som Azure Storage-blobbar eller Azure Data Lake store. I det här fallet behöver inte sink-logik att kontrollera om finns en fil. Om filen som representerar händelsen finns den bara skrivs över med samma data. Annars skapas en ny fil i den beräknade sökvägen.

## <a name="next-steps"></a>Nästa steg

* [Spark Streaming: översikt](apache-spark-streaming-overview.md)
* [Skapa hög tillgänglighet Spark Streaming jobb i YARN](apache-spark-streaming-high-availability.md)
