---
title: Streaming i stor skala i Azure HDInsight
description: Så här använder du dataströmning med skalbara Apache-kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 006310f1a0efa69881bbe6d6ea4403b9c50402e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435385"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Direktuppspelning i skala i HDInsight

Big Data-lösningar i realtid agerar på data som är i rörelse. Vanligtvis är dessa data mest värdefulla vid ankomsttillfället. Om den inkommande dataströmmen blir större än vad som kan hanteras i det ögonblicket kan du behöva begränsa resurserna. Alternativt kan ett HDInsight-kluster skalas upp för att uppfylla din streaminglösning genom att lägga till noder på begäran.

I ett strömmande program genererar en eller flera datakällor händelser (ibland i miljoner per sekund) som måste intas snabbt utan att släppa någon användbar information. De inkommande händelserna hanteras med *stream-buffring*, även kallad *eventköing*, av en tjänst som [Apache Kafka](kafka/apache-kafka-introduction.md) eller [Event Hubs](https://azure.microsoft.com/services/event-hubs/). När du har samlat in händelserna kan du sedan analysera data med hjälp av ett analyssystem i realtid i *dataflödet,* till exempel [Apache Storm](storm/apache-storm-overview.md) eller Apache [Spark Streaming](spark/apache-spark-streaming-overview.md). Bearbetade data kan lagras i långsiktiga lagringssystem, till exempel [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), och visas i realtid på en business intelligence-instrumentpanel, till exempel Power [BI](https://powerbi.microsoft.com), Tableau eller en anpassad webbsida.

![Streamingmönster för Azure HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka tillhandahåller en meddelandetjänst med låg latens med låg latens och är nu en del av Apache-sviten med programvara med öppen källkod (OSS). Kafka använder en publicerings- och prenumerationsmeddelandemodell och lagrar strömmar av partitionerade data på ett säkert sätt i ett distribuerat, replikerat kluster. Kafka skalar linjärt när dataflödet ökar.

Mer information finns i [Introduktion till Apache Kafka på HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm är ett distribuerat, feltolerant beräkningssystem med öppen källkod som är optimerat för bearbetning av dataströmmar i realtid med Hadoop. Kärnenheten med data för en inkommande händelse är en tuppel, som är en oföränderlig uppsättning nyckel-/värdepar. En obunden sekvens av dessa tupel bildar en ström, som kommer från en pip. Pipen radbryts en strömmande datakälla (till exempel Kafka) och avger Tuples. En storm Topologi är en sekvens av omvandlingar på dessa strömmar.

Mer information finns i [Vad är Apache Storm på Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Gnistströmning

Spark Streaming är ett tillägg till Spark, vilket gör att du kan återanvända samma kod som du använder för batchbearbetning. Du kan kombinera både batch- och interaktiva frågor i samma program. Till skillnad från Storm ger Spark Streaming tillståndskänsliga exakt en gång bearbetning semantik. När den används i kombination med [Kafka Direct API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), som säkerställer att alla Kafka-data tas emot av Spark Streaming exakt en gång, är det möjligt att uppnå end-to-end exakt en gång garantier. En av Spark Streamings styrkor är dess feltoleranta funktioner, och återställer felaktiga noder snabbt när flera noder används i klustret.

Mer information finns i [Vad är Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skala ett kluster

Även om du kan ange antalet noder i klustret när du skapar, kanske du vill växa eller krympa klustret så att det matchar arbetsbelastningen. Med alla HDInsight-kluster kan du [ändra antalet noder i klustret](hdinsight-administer-use-portal-linux.md#scale-clusters). Spark-kluster kan tas bort utan förlust av data, eftersom alla data lagras i Azure Storage eller Data Lake Storage.

Det finns fördelar med frikopplingsteknik. Till exempel är Kafka en händelse buffring teknik, så det är mycket IO intensiv och behöver inte mycket processorkraft. I jämförelse är strömprocessorer som Spark Streaming beräkningsintensiva, vilket kräver kraftfullare virtuella datorer. Genom att ha dessa tekniker frikopplade till olika kluster kan du skala dem oberoende av dem samtidigt som du bäst använder de virtuella datorerna.

### <a name="scale-the-stream-buffering-layer"></a>Skala buffertlagret för strömmen

Stream-buffringsteknikerna Event Hubs och Kafka använder båda partitioner och konsumenter läser från dessa partitioner. Skalning av indatadataflödet kräver att antalet partitioner skalas upp, och att lägga till partitioner ger ökande parallellitet. I Event Hubs kan partitionsantalet inte ändras efter distributionen så det är viktigt att börja med målskalan i åtanke. Med Kafka är det möjligt att [lägga till partitioner](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), även när Kafka bearbetar data. Kafka tillhandahåller ett verktyg för att `kafka-reassign-partitions.sh`tilldela om partitioner, . HDInsight tillhandahåller ett verktyg för `rebalance_rackaware.py` [omfördelning av partitionsrepliker](https://github.com/hdinsight/hdinsight-kafka-tools). Detta ombalanseringsverktyg `kafka-reassign-partitions.sh` anropar verktyget på ett sådant sätt att varje replik är i en separat feldomän och uppdatera domän, vilket gör Kafka rack medveten och ökar feltolerans.

### <a name="scale-the-stream-processing-layer"></a>Skala bearbetningslagret för ström

Både Apache Storm och Spark Streaming stöder att lägga till arbetsnoder i sina kluster, även när data bearbetas.

Om du vill dra nytta av nya noder som lagts till genom skalning storm, måste du balansera alla Storm topologier startade innan klustret storlek ökade. Denna ombalansering kan göras med hjälp av Storm webbgränssnitt eller dess CLI. Mer information finns i [Apache Storm-dokumentationen](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark använder tre nyckelparametrar för att konfigurera `spark.executor.instances` `spark.executor.cores`sin `spark.executor.memory`miljö, beroende på programkrav: , och . En *executor* är en process som startas för ett Spark-program. En executor körs på arbetarnoden och ansvarar för att utföra programmets uppgifter. Standardantalet köror och körorstorlekar för varje kluster beräknas baserat på antalet arbetsnoder och arbetarnodstorleken. Dessa nummer lagras `spark-defaults.conf`i filen på varje klusterhuvudnod.

Dessa tre parametrar kan konfigureras på klusternivå, för alla program som körs i klustret och kan också anges för varje enskilt program. Mer information finns i [Hantera resurser för Apache Spark-kluster](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa och övervaka en Apache Storm-topologi i Azure HDInsight](storm/apache-storm-quickstart.md)
* [Exempeltopologier för Apache Storm på HDInsight](storm/apache-storm-example-topology.md)
* [Introduktion till Apache Spark på HDInsight](spark/apache-spark-overview.md)
* [Kom igång med Apache Kafka i HDInsight](kafka/apache-kafka-get-started.md)
