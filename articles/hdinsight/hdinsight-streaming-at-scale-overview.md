---
title: "Strömning skalanpassat i Azure HDInsight | Microsoft Docs"
description: "Hur du använder data strömning med skalbara HDInsight-kluster."
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
ms.date: 01/19/2018
ms.author: ramoha
ms.openlocfilehash: 46b5723805ab5d8bc1cf5b5183d9501cd3e4e3a2
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="streaming-at-scale-in-hdinsight"></a>Strömning i skala i HDInsight

Realtid stordatalösningar fungerar på data i rörelse. Dessa data är vanligtvis värdefullaste på dess ankomsttid. Om den inkommande dataströmmen blir större än vad som kan hanteras för tillfället kan behöva du begränsa resurser ner. Ett HDInsight-kluster kan du skala upp för att uppfylla din strömmande lösning genom att lägga till noder på begäran.

I ett program som strömmande genererar en eller flera datakällor händelser (ibland i miljoner per sekund) som behöver vara inhämtas snabbt utan att släppa användbar information. Inkommande händelser hanteras med *dataströmmen buffring*, även kallat *händelse queuing*, av en tjänst som [Kafka](kafka/apache-kafka-introduction.md) eller [Händelsehubbar](https://azure.microsoft.com/services/event-hubs/). När du har samlat in händelserna som du sedan kan analysera data med ett system för analys i realtid i den *dataströmmen bearbetning* layer, till exempel [Storm](storm/apache-storm-overview.md) eller [Spark Streaming](spark/apache-spark-streaming-overview.md). Bearbetade data kan lagras i långsiktiga lagringssystem som [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/), och visas i realtid på en business intelligence-instrumentpanel som [Power BI](https://powerbi.microsoft.com), Tableau eller en egen webbsida .

![HDInsight strömning mönster](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka ger en hög genomströmning, låg latens message queueing-tjänsten och är nu en del av Apache-paketet för öppen källa programvara (OSS). Kafka använder en publicera och prenumerera på meddelanden modell och lagrar dataströmmar partitionerade på ett säkert sätt i ett distribuerat, replikerad kluster. Kafka skalas linjärt ökar genomströmningen.

Mer information finns i [introduktion till Apache Kafka på HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm är ett system för beräkningar i distribuerat, feltolerant, öppen källkod som är optimerad för bearbetning av dataströmmar i realtid med Hadoop. Core är data för inkommande händelse en tuppel som är en uppsättning nyckel/värde-par som inte ändras. En unbounded sekvens av formulären Tupplar en dataström som kommer från en kanal. Kanal packar en strömmande datakälla (till exempel Kafka) och genererar Tupplar. Ett storm-topologi är en sekvens omformningar på dessa strömmar.

Mer information finns i [vad är Apache Storm på Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming är ett tillägg till Spark, där du kan återanvända samma koden som du använder för batch-bearbetning. Du kan kombinera batch- och interaktiva frågor i samma program. Till skillnad från Storm, Spark Streaming ger stateful exakt-när bearbetningen semantik. När den används tillsammans med den [Kafka direkt API](http://spark.apache.org/docs/latest/streaming-kafka-integration.html), som säkerställer att alla Kafka data tas emot av Spark Streaming exakt en gång, är det möjligt att uppnå slutpunkt till slutpunkt exakt-garanterar en gång. En av Spark Streaming styrkor är dess feltolerant funktioner, återställning av fel noder snabbt när flera noder som används i klustret.

Mer information finns i [vad är Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skala ett kluster

Men du kan ange antalet noder i klustret när du skapar, kanske du vill öka eller minska klustret så att det matchar arbetsbelastningen. Alla HDInsight-kluster kan du [ändra antalet noder i klustret](hdinsight-administer-use-management-portal.md#scale-clusters). Spark-kluster kan släppas utan förlust av data, eftersom alla data som lagras i Azure Storage eller Azure Data Lake Store.

Det finns fördelar Frikoppling tekniker. Till exempel Kafka är en händelse buffring teknik, så att den är mycket i/o-intensiva och behöver inte mycket processorkraft. Jämförelse är stream-processorer, till exempel Spark Streaming beräkningsintensiva, som kräver mer kraftfulla virtuella datorer. Genom att dessa tekniker frikopplad i olika kluster, kan du skala dem separat när bäst använder de virtuella datorerna.

### <a name="scale-the-stream-buffering-layer"></a>Skala dataströmmen buffring lager

Dataströmmen buffring tekniker Händelsehubbar och Kafka båda använder partitioner och konsumenter Läs dessa partitioner. Skalning inkommande genomströmning kräver skala upp antalet partitioner och lägger till partitioner ger ökad parallellitet. I Händelsehubbar, kan antalet partitioner inte ändras efter distributionen så att det är viktigt att börja med skalan mål i åtanke. Med Kafka, är det möjligt att [lägga till partitioner](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), även medan Kafka bearbetar data. Kafka ger ett verktyg för att omtilldela partitioner, `kafka-reassign-partitions.sh`. HDInsight tillhandahåller en [partition replik ombalansering verktyget](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Rebalancing verktyget anropar den `kafka-reassign-partitions.sh` verktyget så att varje replik som är i en separat feldomän och uppdateringsdomän, gör Kafka rack medveten och ökad feltolerans.

### <a name="scale-the-stream-processing-layer"></a>Skala dataströmmen bearbetning lagret

Apache Storm och Spark Streaming stöd för att lägga till arbetsnoder till deras kluster, även när data bearbetas.

Om du vill dra nytta av nya noder som lagts till via skalning Storm, behöver du balansera alla Storm-topologier som startats innan klusterstorleken ökades. Den här ombalansering kan göras med hjälp av Storm web Användargränssnittet eller dess CLI. Mer information finns i [Apache Storm dokumentationen](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark använder tre viktiga parametrar för att konfigurera sin miljö, beroende på kraven för application: `spark.executor.instances`, `spark.executor.cores`, och `spark.executor.memory`. En *utföraren* är en process som startas för ett Spark-program. En utförare körs på arbetsnoden och ansvarar för att utföra uppgifter i programmet. Standardantalet executors och utföraren storleken för varje kluster beräknas baserat på antalet arbetarnoder och nodstorlek worker. Dessa siffror lagras i den `spark-defaults.conf`fil på varje klustrets huvudnod.

De här tre parametrarna kan konfigureras på klusternivå för alla program som körs på klustret och kan också anges för varje enskilt program. Mer information finns i [hantera resurser för Apache Spark-kluster](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Apache Storm på HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Exempeltopologier för Apache Storm på HDInsight](storm/apache-storm-example-topology.md)
* [Introduktion till Spark i HDInsight](spark/apache-spark-overview.md)
* [Börja med Apache Kafka på HDInsight](kafka/apache-kafka-get-started.md)