---
title: Log Analytics för Apache Kafka – Azure HDInsight
description: Lär dig hur du använder Log Analytics för att analysera loggar från Apache Kafka-kluster på Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 69eaa0028f1115cafbd1ed28b66940d7faaed062
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608553"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analysera loggar för Apache Kafka på HDInsight

Lär dig hur du använder Log Analytics för att analysera loggar som genereras av Apache Kafka på HDInsight.

## <a name="enable-log-analytics-for-apache-kafka"></a>Aktivera logganalys för Apache Kafka

Stegen för att aktivera Log Analytics för HDInsight är desamma för alla HDInsight-kluster. Använd följande länkar om du vill lära dig att skapa och konfigurera nödvändiga tjänster:

1. Skapa en Log Analytics-arbetsyta. Mer information finns i den [Kom igång med Log Analytics-arbetsytan](https://docs.microsoft.com/azure/log-analytics) dokumentet.

2. Skapa en Kafka på HDInsight-kluster. Mer information finns i den [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md) dokumentet.

3. Konfigurera Kafka-kluster för att använda Log Analytics. Mer information finns i den [med Log Analytics kan du övervaka HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentet.

    > [!NOTE]  
    > Du kan också konfigurera kluster för att använda Log Analytics med hjälp av den `Enable-AzureRmHDInsightOperationsManagementSuite` cmdlet. Denna cmdlet kräver följande information:
    >
    > * HDInsight-klustrets namn.
    > * Arbetsyte-ID för Log Analytics. Du hittar ID för arbetsyta i Log Analytics-arbetsytan.
    > * Den primära nyckeln för Log Analytics-anslutning. För att hitta den primära nyckeln, öppen arbetsytan i Azure portal, Välj __avancerade inställningar__ menyn till vänster. Avancerade inställningar, Välj __anslutna källor__>__Linux-servrar__.


> [!IMPORTANT]  
> Det kan ta cirka 20 minuter innan data är tillgängliga för Log Analytics.

## <a name="query-logs"></a>Frågeloggar

1. Från den [Azure-portalen](https://portal.azure.com), Välj Log Analytics-arbetsytan.

2. Välj __Loggsöknings__. Härifrån kan du söka efter data som samlas in från Kafka. Här följer några exempel genomsöks:

    * Diskanvändning: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * CPU-användning: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Inkommande meddelanden per sekund: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * Inkommande byte per sekund: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * Utgående byte per sekund: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]  
    > Ersätt värdena fråga med din specifika information för klustret. Till exempel `ClusterName_s` måste anges till namnet på klustret. `HostName_s` måste anges till domännamnet för en underordnad nod i klustret.

    Du kan också ange `*` att söka alla typer som är inloggad. För närvarande är följande loggar tillgängliga för frågor:

    | Loggtyp | Beskrivning |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | mått\_kafka\_CL | Kafka JMX-mätvärden |

    ![Bild av CPU-användning sökningen](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Nästa steg

Mer information om Log Analytics finns det [Kom igång med Log Analytics-arbetsytan](../../log-analytics/log-analytics-get-started.md) dokumentet.

Mer information om hur du arbetar med Apache Kafka finns i följande dokument:

 * [Spegling Apache Kafka mellan HDInsight-kluster](apache-kafka-mirroring.md)
 * [Öka skalbarheten för Apache Kafka på HDInsight](apache-kafka-scalability.md)
 * [Använda Apache Spark-strömning (DStreams) med Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Använda Apache Spark structured streaming med Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
