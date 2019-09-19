---
title: Fråga Azure Monitor loggar för att övervaka Azure HDInsight-kluster
description: Lär dig hur du kör frågor på Azure Monitor loggar för att övervaka jobb som körs i ett HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 51344ff7381b6392870b1fd0e331eed38a33915d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103507"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Fråga Azure Monitor loggar för att övervaka HDInsight-kluster

Lär dig några grundläggande scenarier för hur du använder Azure Monitor loggar för att övervaka Azure HDInsight-kluster:

* [Analysera HDInsight-kluster mått](#analyze-hdinsight-cluster-metrics)
* [Sök efter vissa logg meddelanden](#search-for-specific-log-messages)
* [Skapa händelse aviseringar](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

Du måste ha konfigurerat ett HDInsight-kluster för att använda Azure Monitor loggar och lagt till de HDInsight-företagsspecifika Azure Monitor loggar övervaknings lösningar på arbets ytan. Instruktioner finns i [använda Azure Monitor loggar med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analysera HDInsight-kluster mått

Lär dig hur du söker efter vissa mått för ditt HDInsight-kluster.

1. Öppna arbets ytan Log Analytics som är kopplad till ditt HDInsight-kluster från Azure Portal.
1. Välj panelen **loggs ökning** .
1. Skriv följande fråga i sökrutan om du vill söka efter alla mått för alla tillgängliga mått för alla HDInsight-kluster som kon figurer ATS att använda Azure Monitor loggar och välj sedan **Kör**.

        search *

    ![Apache Ambari Analytics Sök i alla mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Sök alla mått")

    Utdata ska se ut så här:

    ![Log Analytics Sök i alla mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Sök i alla mått resultat")

1. I den vänstra rutan under **typ**väljer du ett mått som du vill göra djupgåendet i djupet och väljer sedan **Använd**. Följande skärm bild visar `metrics_resourcemanager_queue_root_default_CL` typen är markerad.

    > [!NOTE]  
    > Du kan behöva välja knappen **[+] mer** för att hitta det mått du söker. Knappen **tillämpa** är också längst ned i listan, så du måste rulla nedåt för att se den.

    Observera att frågan i text rutan ändras till en som visas i den markerade rutan på följande skärm bild:

    ![Sök efter vissa mått i Log Analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Sök efter vissa mått")

1. Att gå djupare i det här måttet. Du kan till exempel förfina den befintliga utdatan baserat på genomsnitt av resurser som används under ett 10-minuters intervall, kategoriserade efter kluster namn med hjälp av följande fråga:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

1. I stället för att förfina baserat på genomsnittet av de resurser som används kan du använda följande fråga för att förfina resultaten baserat på när de maximala resurserna användes (samt nittionde och 95 percentil) i ett 10-minuters fönster:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Sök efter vissa logg meddelanden

Lär dig hur du söker efter fel meddelanden under en bestämd tids period. Stegen här är bara ett exempel på hur du kan komma till det fel meddelande som du är intresse rad av. Du kan använda vilken egenskap som helst som är tillgänglig för att söka efter de fel som du försöker hitta.

1. Öppna arbets ytan Log Analytics som är kopplad till ditt HDInsight-kluster från Azure Portal.
2. Välj panelen **loggs ökning** .
3. Skriv följande fråga för att söka efter alla fel meddelanden för alla HDInsight-kluster som kon figurer ATS att använda Azure Monitor loggar och välj sedan **Kör**.

         search "Error"

    Du får se utdata som följande utdata:

    ![Azure Portal loggs öknings fel](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Sök alla fel i utdata")

4. I den vänstra rutan under **typ** kategori väljer du den typ av fel som du vill ha i djupet och väljer sedan **Använd**.  Observera att resultaten är förfinade för att bara Visa felet av den typ som du har valt.

5. Du kan gå djupare i den här fel listan genom att använda alternativen som finns i den vänstra rutan. Exempel:

    - Så här visar du fel meddelanden från en speciell arbetsnod:

        ![Sök efter vissa fel output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Sök efter vissa fel output1")

    - För att se ett fel inträffade vid en viss tidpunkt:

        ![Sök efter vissa fel output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Sök efter vissa fel output2")

6. För att se det aktuella felet. Du kan välja **[+] Visa mer** för att titta på det faktiska fel meddelandet.

    ![Sök efter vissa fel output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Sök efter vissa fel output3")

## <a name="create-alerts-for-tracking-events"></a>Skapa aviseringar för spårning av händelser

Det första steget för att skapa en avisering är att komma till en fråga baserat på vilken aviseringen utlöses. Du kan använda alla frågor som du vill skapa en avisering för.

1. Öppna arbets ytan Log Analytics som är kopplad till ditt HDInsight-kluster från Azure Portal.
2. Välj panelen **loggs ökning** .
3. Kör följande fråga som du vill skapa en avisering på och välj sedan **Kör**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Frågan innehåller en lista över misslyckade program som körs på HDInsight-kluster.

4. Välj **ny varnings regel** överst på sidan.

    ![Ange fråga för att skapa en alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Ange fråga för att skapa en alert1")

5. I fönstret **Skapa regel** anger du frågan och annan information för att skapa en avisering och väljer sedan **skapa aviserings regel**.

    ![Ange fråga för att skapa en alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Ange fråga för att skapa en alert2")

Redigera eller ta bort en befintlig avisering:

1. Öppna arbets ytan Log Analytics från Azure Portal.
2. Välj **avisering**på menyn till vänster.
3. Välj den avisering som du vill redigera eller ta bort.
4. Du har följande alternativ: **Spara**, **ta**bort, **inaktivera**och **ta bort**.

    ![HDInsight Azure Monitor loggar varning ta bort redigera](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Mer information finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Se också

* [Skapa anpassade vyer med hjälp av View Designer i Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../azure-monitor/platform/alerts-metric.md)
