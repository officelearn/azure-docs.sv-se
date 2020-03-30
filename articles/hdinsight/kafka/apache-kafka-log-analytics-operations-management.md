---
title: Azure Monitor loggar för Apache Kafka - Azure HDInsight
description: Lär dig hur du använder Azure Monitor-loggar för att analysera loggar från Apache Kafka-kluster på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471188"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analysera loggar för Apache Kafka i HDInsight

Lär dig hur du använder Azure Monitor-loggar för att analysera loggar som genereras av Apache Kafka på HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Loggar plats

Apache Kafka loggar i klustret finns på `/var/log/kafka`. Kafka-loggar sparas inte eller sparas över klustrets livscykel, oavsett om hanterade diskar används. I följande tabell visas tillgängliga loggar.

|Logga |Beskrivning |
|---|---|
|kafka.out (kafka.out)|stdout och stderr av Kafka processen. Du hittar Kafka start och avstängning loggar i den här filen.|
|server.log|Den viktigaste Kafka serverloggen. Alla Kafka mäklare loggar hamna här.|
|controller.log|Controller loggar om mäklaren fungerar som controller.|
|statechange.log|Alla tillståndsändringar till mäklare loggas i den här filen.|
|kafka-gc.log|Kafka Garbage Collection statistik.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Aktivera Azure Monitor-loggar för Apache Kafka

Stegen för att aktivera Azure Monitor-loggar för HDInsight är desamma för alla HDInsight-kluster. Använd följande länkar för att förstå hur du skapar och konfigurerar de tjänster som krävs:

1. Skapa en log analytics-arbetsyta. Mer information finns i dokumentet [Loggar i Azure Monitor.](../../azure-monitor/platform/data-platform-logs.md)

2. Skapa en Kafka på HDInsight-kluster. Mer information finns i [dokumentet Starta med Apache Kafka i HDInsight.](apache-kafka-get-started.md)

3. Konfigurera Kafka-klustret så att det använder Azure Monitor-loggar. Mer information finns i [Använda Azure Monitor-loggarna för att övervaka HDInsight-dokument.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Det kan ta cirka 20 minuter innan data är tillgängliga för Azure Monitor-loggar.

## <a name="query-logs"></a>Frågeloggar

1. Välj din Log Analytics-arbetsyta på [Azure-portalen.](https://portal.azure.com)

2. Välj **Loggar**under **Allmänt**på den vänstra menyn . Härifrån kan du söka de uppgifter som samlats in från Kafka. Ange en fråga i frågefönstret och välj sedan **Kör**. Följande är några exempel sökningar:

* Användning av disk:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU-användning:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Inkommande meddelanden per sekund: `your_kafka_cluster_name` (Ersätt med klusternamnet.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Inkommande byte per sekund: `wn0-kafka` (Ersätt med ett värdnamn för en arbetarnod.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Utgående byte per sekund: (Ersätt `your_kafka_cluster_name` med klusternamnet.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Du kan `*` också ange för att söka i alla typer som loggas. För närvarande är följande loggar tillgängliga för frågor:

    | Typ av logg | Beskrivning |
    | ---- | ---- |
    | logga\_kafkaserver\_CL | Kafka mäklare server.log |
    | log\_kafkacontroller\_CL | Kafka mäklare controller.log |
    | mätvärden\_kafka\_CL | Kafka JMX-mätvärden |

    ![Apache kafka logg analys cpu användning](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Monitor finns i [Azure Monitor översikt](../../log-analytics/log-analytics-get-started.md)och Fråge Azure [Monitor-loggar för att övervaka HDInsight-kluster](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Mer information om hur du arbetar med Apache Kafka finns i följande dokument:

* [Spegla Apache Kafka mellan HDInsight-kluster](apache-kafka-mirroring.md)
* [Öka omfattningen av Apache Kafka på HDInsight](apache-kafka-scalability.md)
* [Använd Apache Spark streaming (DStreams) med Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Använd Apache Spark strukturerad streaming med Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
