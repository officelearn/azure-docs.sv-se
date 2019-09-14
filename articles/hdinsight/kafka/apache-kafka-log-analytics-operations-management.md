---
title: Azure Monitor loggar för Apache Kafka – Azure HDInsight
description: Lär dig hur du använder Azure Monitor loggar för att analysera loggar från Apache Kafka-kluster på Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 44eea1bc6390e743aff104550e5b6d7e97c45929
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960125"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analysera loggar för Apache Kafka i HDInsight

Lär dig hur du använder Azure Monitor loggar för att analysera loggar som genereras av Apache Kafka i HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Aktivera Azure Monitor loggar för Apache Kafka

Stegen för att aktivera Azure Monitor loggar för HDInsight är desamma för alla HDInsight-kluster. Använd följande länkar om du vill veta hur du skapar och konfigurerar nödvändiga tjänster:

1. Skapa en Log Analytics-arbetsyta. Mer information finns i [loggarna i Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) -dokument.

2. Skapa en Kafka på HDInsight-kluster. Mer information finns i dokumentet [starta med Apache Kafka på HDInsight](apache-kafka-get-started.md) .

3. Konfigurera Kafka-klustret så att det använder Azure Monitor loggar. Mer information finns i avsnittet [använda Azure Monitor loggar för att övervaka HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) -dokument.

> [!IMPORTANT]  
> Det kan ta ungefär 20 minuter innan data är tillgängliga för Azure Monitor loggar.

## <a name="query-logs"></a>Fråga efter loggar

1. Välj arbets ytan Log Analytics från [Azure Portal](https://portal.azure.com).

2. I den vänstra menyn under **Allmänt**väljer du **loggar**. Härifrån kan du söka i data som samlas in från Kafka. Ange en fråga i frågefönstret och välj sedan **Kör**. Följande är några exempel på sökningar:

* Disk användning:

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
    > Ersätt värdena i frågan med din klustrade information. Till exempel `ClusterName_s` måste vara inställt på namnet på klustret. `HostName_s`måste anges till domän namnet för en arbetsnoden i klustret.
    
    Du kan också ange `*` om du vill söka efter alla typer som loggas. För närvarande finns följande loggar för frågor:
    
    | Loggtyp | Beskrivning |
    | ---- | ---- |
    | Logga\_kafkaserver\_cl | Kafka Broker-Server. log |
    | log\_kafkacontroller\_CL | Kafka Broker-styrenhet. log |
    | \_mått Kafkacl\_ | Kafka JMX-mått |
    
    ![Bild av CPU-användnings sökning](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Nästa steg

Mer information om Azure Monitor finns i [Azure Monitor översikt](../../log-analytics/log-analytics-get-started.md)och [fråga Azure Monitor loggar för att övervaka HDInsight-kluster](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Mer information om hur du arbetar med Apache Kafka finns i följande dokument:

* [Spegla Apache Kafka mellan HDInsight-kluster](apache-kafka-mirroring.md)
* [Öka skalbarheten för Apache Kafka i HDInsight](apache-kafka-scalability.md)
* [Använda Apache Spark strömning (DStreams) med Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Använd Apache Spark strukturerad strömning med Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
