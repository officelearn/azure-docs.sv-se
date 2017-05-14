---
title: "Introduktion till Apache Kafka på HDInsight | Microsoft Docs"
description: "Läs mer om Apache Kafka på HDInsight. Vad detta är, vad det används till och var du hittar exempel och information om att komma igång."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: ca48abcdc9f9d05648a4b03bdb5fec7b4a5b7cce
ms.contentlocale: sv-se
ms.lasthandoff: 05/04/2017

---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>Introduktion till Apache Kafka på HDInsight (förhandsversion)

[Apache Kafka](https://kafka.apache.org) är en distribuerad direktuppspelningsplattform med öppen källkod som kan användas för att skapa realtidsuppspelade datapipelines och program. Kafka tillhandahåller även funktionen för asynkron meddelandekö som liknar en meddelandekö där du kan publicera och prenumerera på namngivna dataströmmar. Kafka på HDInsight erbjuder en hanterad, maximalt skalbar och högtillgänglig tjänst i Microsoft Azure-molnet.

## <a name="why-use-kafka-on-hdinsight"></a>Varför använda Kafka på HDInsight?

Kafka tillhandahåller följande funktioner:

* Meddelandemönster av typen publicera-prenumerera: Kafka innehåller en producent-API för publicering av poster till ett Kafka-ämne. Konsument-API används när det finns en prenumererar på ett ämne.

* Dataströmsbearbetning: Kafka används ofta med Apache Storm eller Spark för bearbetning av dataströmmen i realtid. Kafka 0.10.0.0 (HDInsight version 3.5) införde en strömmande API som gör att du kan skapa direktuppspelade lösningar utan Storm eller Spark.

* Vågrät skala: Kafka partitionerar dataströmmar mellan noderna i HDInsight-klustret. Konsumentprocesser kan associeras med enskilda partitioner för att ge belastningsutjämning när poster används.

* Leverans i ordning: Inom varje partition lagras posterna i strömmen i den ordning som de togs emot. Genom att associera en konsumentprocess per partition bearbetas posterna garanterat i ordning.

* Feltoleranta: Partitioner kan replikeras mellan noder för att ge feltolerans.

## <a name="use-cases"></a>Användningsfall

* **Meddelanden**: Eftersom den stöder meddelandemönstret publicera-prenumerera används Kafka ofta som en asynkron meddelandekö.

* **Aktivitetsspårning**: Eftersom Kafka innehåller i ordningsbaserad loggning av poster kan den användas för att spåra och återskapa aktiviteter. Till exempel användaråtgärder på en webbplats eller i ett program.

* **Sammanställning**: Med hjälp av strömbearbetning kan du sammanställa information från olika strömmar för att kombinera och centralisera information till användningsdata.

* **Omvandling**: Med dataströmsbearbetning kan du kombinera och utöka data från flera inkommande avsnitt i ett eller flera utdataämnen.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta om hur du använder Apache Kafka på HDInsight:

* [Kom igång med Kafka på HDInsight](hdinsight-apache-kafka-get-started.md)

* [Använd MirrorMaker för att skapa en replik av Kafka på HDInsight](hdinsight-apache-kafka-mirroring.md)

* [Använda Apache Storm med Kafka på HDInsight](hdinsight-apache-storm-with-kafka.md)

* [Använda Apache Spark med Kafka på HDInsight](hdinsight-apache-spark-with-kafka.md)

* [Ansluta till Kafka via ett trådlöst Azure-nätverk](hdinsight-apache-kafka-connect-vpn-gateway.md)
