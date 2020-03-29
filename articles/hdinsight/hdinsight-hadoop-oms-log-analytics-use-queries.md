---
title: Fråga Azure Monitor-loggar för att övervaka Azure HDInsight-kluster
description: Lär dig hur du kör frågor på Azure Monitor-loggar för att övervaka jobb som körs i ett HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803845"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Fråga Azure Monitor-loggar för att övervaka HDInsight-kluster

Lär dig några grundläggande scenarier om hur du använder Azure Monitor-loggar för att övervaka Azure HDInsight-kluster:

* [Analysera mätvärden för HDInsight-kluster](#analyze-hdinsight-cluster-metrics)
* [Skapa händelseaviseringar](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

Du måste ha konfigurerat ett HDInsight-kluster för att kunna använda Azure Monitor-loggar och lagt till hdinsight-klusterspecifika Azure Monitor-loggövervakningslösningar på arbetsytan. Instruktioner finns i [Använda Azure Monitor-loggar med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analysera mätvärden för HDInsight-kluster

Lär dig hur du letar efter specifika mått för ditt HDInsight-kluster.

1. Öppna arbetsytan Log Analytics som är associerad med ditt HDInsight-kluster från Azure-portalen.
1. Under **Allmänt**väljer du **Loggar**.
1. Skriv följande fråga i sökrutan för att söka efter alla mått för alla tillgängliga mått för alla HDInsight-kluster som konfigurerats för att använda Azure Monitor-loggar och välj sedan **Kör**. Granska resultaten.

    ```kusto
    search *
    ```

    ![Apache Ambari analytics sök alla mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Sök i alla mått")

1. Välj fliken **Filter** på den vänstra menyn.

1. Under **Text**väljer du **Pulsslag**. Välj sedan **Använd & Kör**.

    ![logganalys sök specifika mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Sök efter specifika mått")

1. Observera att frågan i textrutan ändras till:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Du kan gräva djupare med hjälp av alternativen i den vänstra menyn. Ett exempel:

    - Så här visar du loggar från en viss nod:

        ![Sök efter specifika felutdata1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Sök efter specifika felutdata1")

    - Så här visar du loggar vid vissa tidpunkter:

        ![Sök efter specifika feldata2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Sök efter specifika feldata2")

1. Välj **Använd & Kör** och granska resultaten. Observera också att frågan uppdaterades till:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Ytterligare exempelfrågor

En exempelfråga baserad på medelvärdet av resurser som används i ett 10-minutersintervall, kategoriserat efter klusternamn:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

I stället för att förfina baserat på medelvärdet av de resurser som används kan du använda följande fråga för att förfina resultaten baserat på när de maximala resurserna användes (samt 90:e och 95:e percentilen) i ett 10-minutersfönster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Skapa aviseringar för att spåra händelser

Det första steget för att skapa en avisering är att komma fram till en fråga som baseras på vilken aviseringen utlöses. Du kan använda alla frågor som du vill skapa en avisering.

1. Öppna arbetsytan Log Analytics som är associerad med ditt HDInsight-kluster från Azure-portalen.
1. Under **Allmänt**väljer du **Loggar**.
1. Kör följande fråga som du vill skapa en avisering för och välj sedan **Kör**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    Frågan innehåller en lista över misslyckade program som körs på HDInsight-kluster.

1. Välj **Ny varningsregel** högst upp på sidan.

    ![Ange fråga för att skapa en avisering1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Ange fråga för att skapa en avisering1")

1. I **fönstret Skapa regel** anger du frågan och annan information för att skapa en avisering och väljer sedan **Skapa aviseringsregel**.

    ![Ange fråga för att skapa en avisering2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Ange fråga för att skapa en avisering2")

### <a name="edit-or-delete-an-existing-alert"></a>Redigera eller ta bort en befintlig avisering

1. Öppna log analytics-arbetsytan från Azure-portalen.

1. Välj **Aviseringar**under **Övervakning**på den vänstra menyn .

1. Längst upp väljer du **Hantera varningsregler**.

1. Markera den avisering som du vill redigera eller ta bort.

1. Du har följande alternativ: **Spara,** **Ignorera**, **Inaktivera**och **Ta bort**.

    ![HDInsight Azure Monitor loggar varning ta bort redigera](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Mer information finns i [Skapa, visa och hantera måttaviseringar med Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Se även

* [Komma igång med loggfrågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Skapa anpassade vyer med Hjälp av Visa designer i Azure Monitor](../azure-monitor/platform/view-designer.md)
