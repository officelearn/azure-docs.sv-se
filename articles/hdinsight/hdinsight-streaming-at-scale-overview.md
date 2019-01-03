---
title: Direktuppspelning i skala i Azure HDInsight
description: Hur du använder data som strömmas med skalbar HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 77a3685f59c7b15473deda1894f6fd6934fafc1f
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993424"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Direktuppspelning i skala i HDInsight

Lösningar för stordata i realtid agera utifrån data i rörelse. Dessa data är vanligtvis mest värdefulla på dess ankomsttid. Om den inkommande dataströmmen blir större än vad som kan hanteras då kan behöva du begränsa resurser ner. Du kan också skalas ett HDInsight-kluster efter din lösning för liveuppspelning genom att lägga till noder på begäran.

En eller flera datakällor som genererar händelser (ibland i miljoner per sekund) som behöver matas in snabbt utan att släppa någon användbar information i en strömmande program. Inkommande händelser hanteras med *strömbuffring*, kallas även *Händelsekö*, av en tjänst som [Apache Kafka](kafka/apache-kafka-introduction.md) eller [Händelsehubbar](https://azure.microsoft.com/services/event-hubs/). När du har samlat in händelserna som du kan sedan analysera data med ett system för analys i realtid inom den *strömbearbetning* olika lager, till exempel [Apache Storm](storm/apache-storm-overview.md) eller [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). Bearbetade data kan lagras i långsiktig lagringssystem som [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), och visas i realtid på en instrumentpanel för business intelligence, till exempel [Power BI](https://powerbi.microsoft.com), Tableau eller en anpassad webbplats sidan.

![HDInsight Streaming-mönster](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka ger en hög genomströmning, låg latens meddelande kötjänst och är nu en del av programsviten Apache med öppen källkod programvara (OSS). Kafka använder en publicera och prenumerera på meddelanden modell och lagrar dataströmmar partitionerade på ett säkert sätt i en distribuerad, replikerade kluster. Kafka skalas linjärt när dataflödet ökar.

Mer information finns i [introduktion till Apache Kafka på HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm är ett system för beräkningar för distribuerat, feltolerant, öppen källkod som är optimerad för bearbetning av dataströmmar i realtid med Hadoop. Core-enhet med data för en inkommande händelse är en tuppel, vilket är en uppsättning nyckel/värde-par som inte kan ändras. En obundna sekvens av formulären Tupplar Stream, som kommer från en kanal. Kanal omsluter en strömmande datakälla (till exempel Kafka) och genererar Tupplar. Ett storm-topologi är en sekvens av transformationer i dessa strömmar.

Mer information finns i [vad är Apache Storm på Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark-strömning

Spark Streaming är en utökning av Spark, vilket gör att du kan återanvända samma kod som du använder för batchbearbetning. Du kan kombinera batch- och interaktiva frågor i samma program. Till skillnad från Storm, Spark Streaming ger tillståndskänslig exakt-när bearbetning av semantik. När de används i kombination med den [Kafka direkta API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), som säkerställer att alla Kafka-data tas emot av Spark Streaming exakt en gång, är det möjligt att uppnå slutpunkt till slutpunkt exakt-garanterar en gång. En av Spark Streaming styrkor är dess feltolerant funktioner, återställa felaktiga noder snabbt när flera noder som används i klustret.

Mer information finns i [vad är Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skala ett kluster

Men du kan ange antalet noder i klustret när du skapar, kanske du vill öka eller minska klustret så att det matchar arbetsbelastningen. Alla HDInsight-kluster kan du [ändra antalet noder i klustret](hdinsight-administer-use-management-portal.md#scale-clusters). Ta kan bort Spark-kluster utan någon dataförlust, eftersom alla data lagras i Azure Storage eller Data Lake Storage.

Det finns fördelar med att Frikoppling tekniker. Till exempel Kafka är en händelse som buffring teknik, så det är mycket i/o-intensiva och behöver inte mycket processorkraft. Däremot är strömprocessorer, till exempel Spark Streaming beräkningsintensiva, som kräver mer kraftfulla virtuella datorer. Genom att låta dessa tekniker frikopplad i olika kluster, kan du skala dem oberoende av varandra när av de virtuella datorerna.

### <a name="scale-the-stream-buffering-layer"></a>Skala stream buffring lager

Dataströmmen buffring tekniker Event Hubs och Kafka båda använder partitioner och konsumenter Läs dessa partitioner. Skala inkommande dataflöde kräver att skala upp antalet partitioner att lägga till partitioner ger ökade parallellitet. I Event Hubs, kan antalet partitioner inte ändras efter distributionen så det är viktigt att börja med en mål-skala i åtanke. Med Kafka, är det möjligt att [Lägg till partitioner](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), även medan Kafka bearbetar data. Kafka tillhandahåller ett verktyg för att omtilldela partitioner, `kafka-reassign-partitions.sh`. HDInsight ger en [partition repliken ombalansering verktyget](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Det här balansering verktyget anropar den `kafka-reassign-partitions.sh` verktyget så att varje replik är i en separat feldomän och uppdateringsdomän, vilket gör Kafka rack medveten och att öka feltolerans.

### <a name="scale-the-stream-processing-layer"></a>Skala bearbetning dataströmlagret

Apache Storm och Spark Streaming stöd för att lägga till arbetsnoder till sina kluster, även medan data bearbetas.

Om du vill dra nytta av nya noder som har lagts till via skalning Storm, behöver du ombalansera de eventuella Storm-topologier som startats innan klusterstorleken ökades. Den här ombalansering kan göras med hjälp av Storm web UI eller dess CLI. Mer information finns i den [Apache Storm-dokumentationen](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark använder tre viktiga parametrar för att konfigurera sin miljö, beroende på programkrav: `spark.executor.instances`, `spark.executor.cores`, och `spark.executor.memory`. En *executor* är en process som har startats för ett Spark-program. En executor körs på arbetsnoden och ansvarar för att utföra uppgifter för det programmet. Standardvärdet för antal executors och executor storleken för varje kluster beräknas baserat på antalet arbetsnoder och nodstorlek worker. Talen lagras i den `spark-defaults.conf`fil på varje klustrets huvudnod.

De här tre parametrarna kan konfigureras på klusternivå, för alla program som körs på klustret och kan också anges för varje enskilt program. Mer information finns i [hantera resurser för Apache Spark-kluster](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Apache Storm på HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Exempeltopologier för Apache Storm på HDInsight](storm/apache-storm-example-topology.md)
* [Introduktion till Apache Spark i HDInsight](spark/apache-spark-overview.md)
* [Kom igång med Apache Kafka på HDInsight](kafka/apache-kafka-get-started.md)
