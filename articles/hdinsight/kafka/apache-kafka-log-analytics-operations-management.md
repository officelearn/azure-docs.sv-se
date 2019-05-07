---
title: Azure Monitor-loggar för Apache Kafka – Azure HDInsight
description: Lär dig hur du använder Azure Monitor-loggar för att analysera loggar från Apache Kafka-kluster på Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 1e141aea3b22bfdcb981513f03e595b6c2f15466
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147969"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analysera loggar för Apache Kafka på HDInsight

Lär dig hur du använder Azure Monitor-loggar för att analysera loggar som genereras av Apache Kafka på HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Aktivera Azure Monitor-loggar för Apache Kafka

Stegen för att aktivera Azure Monitor-loggar för HDInsight är desamma för alla HDInsight-kluster. Använd följande länkar om du vill lära dig att skapa och konfigurera nödvändiga tjänster:

1. Skapa en Log Analytics-arbetsyta. Mer information finns i den [loggar i Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) dokumentet.

2. Skapa en Kafka på HDInsight-kluster. Mer information finns i den [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md) dokumentet.

3. Konfigurera Kafka-kluster för att använda Azure Monitor-loggar. Mer information finns i den [Använd Azure Monitor-loggar för att övervaka HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentet.

> [!IMPORTANT]  
> Det kan ta cirka 20 minuter innan data är tillgängliga för Azure Monitor-loggar.

## <a name="query-logs"></a>Frågeloggar

1. Från den [Azure-portalen](https://portal.azure.com), Välj Log Analytics-arbetsytan.

2. I den vänstra menyn under **Allmänt**väljer **loggar**. Härifrån kan du söka efter data som samlas in från Kafka. Ange en fråga i frågefönstret och välj sedan **kör**. Här följer några exempel genomsöks:

* Diskanvändning:

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

* Inkommande meddelanden per sekund:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Inkommande byte per sekund:

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Utgående byte per sekund:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    > [!IMPORTANT]  
    > Ersätt värdena fråga med din specifika information för klustret. Till exempel `ClusterName_s` måste anges till namnet på klustret. `HostName_s` måste anges till domännamnet för en underordnad nod i klustret.
    
    Du kan också ange `*` att söka alla typer som är inloggad. För närvarande är följande loggar tillgängliga för frågor:
    
    | Loggtyp | Beskrivning |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX metrics |
    
    ![Bild av CPU-användning sökningen](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Nästa steg

Mer information om Azure Monitor finns [översikt över Azure Monitor](../../log-analytics/log-analytics-get-started.md), och [fråga Azure Monitor-loggar för att övervaka HDInsight-kluster](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Mer information om hur du arbetar med Apache Kafka finns i följande dokument:

* [Spegling Apache Kafka mellan HDInsight-kluster](apache-kafka-mirroring.md)
* [Öka skalbarheten för Apache Kafka på HDInsight](apache-kafka-scalability.md)
* [Använda Apache Spark-strömning (DStreams) med Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Spark structured streaming med Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
