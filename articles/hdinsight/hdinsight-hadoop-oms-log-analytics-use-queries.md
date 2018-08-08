---
title: Fråga Azure Log Analytics för att övervaka Azure HDInsight-kluster
description: Lär dig hur du kör frågor på Azure Log Analytics för att övervaka jobb som körs i ett HDInsight-kluster.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 2d2de3c2e2b291ec1f5ad170350f19e9e0582eaa
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39602097"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Fråga Azure Log Analytics för att övervaka HDInsight-kluster

Lär dig några grundläggande scenarier för hur du använder Azure Log Analytics för att övervaka Azure HDInsight-kluster:

* [Analysera HDInsight-kluster-mått](#analyze-hdinsight-cluster-metrics)
* [Sök efter specifika loggmeddelanden](#search-for-specific-log-messages)
* [Skapa aviseringar](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Förutsättningar

* Du har konfigurerat ett HDInsight-kluster för att använda Azure Log Analytics och HDInsight klusterspecifika Log Analytics-hanteringslösningar läggs till i arbetsytan. Anvisningar finns i [med Azure Log Analytics med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analysera HDInsight-kluster-mått

Lär dig hur du söker efter specifika mått för ditt HDInsight-kluster.

1. Öppna OMS-arbetsytan som är kopplad till ditt HDInsight-kluster från Azure-portalen.
2. Välj den **Loggsökning** panelen.
3. Skriver du följande fråga i sökrutan för att söka efter alla mått för alla tillgängliga mått för alla HDInsight-kluster som konfigurerats att använda Azure Log Analytics och välj sedan **kör**.

        search *

    ![Sök alla mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Sök alla mått")

    Utdata bör se ut:

    ![Sök alla utdata för mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Sök alla utdata för mått")

5. I den vänstra rutan under **typ**, Välj ett mått som du vill gå på djupet i och välj sedan **tillämpa**. Följande skärmbild visar de `metrics_resourcemanager_queue_root_default_CL` typ har valts.

    > [!NOTE]
    > Du kan behöva välja den **[+] mer** för att hitta det mått som du letar efter. Dessutom den **tillämpa** är knappen längst ned i listan så att du måste rulla ned för att se den.

    Observera att frågan i textrutan ändras till det visas i rutan markerade i följande skärmbild:

    ![Sök efter specifika mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Sök efter specifika mått")

6. Att fördjupa oss i den här specifika mått. Du kan exempelvis begränsa befintliga utdata baserat på medelvärdet för resurser som används i ett 10 minuters intervall, kategoriserade efter klustrets namn med följande fråga:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. I stället för att förfina baserat på genomsnittliga resurser som används kan du använda följande fråga för att förfina resultatet baserat på när de maximala som användes (samt 90 och den 95: e percentilen) i ett fönster med 10 minuters:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Sök efter specifika loggmeddelanden

Lär dig hur du söker felmeddelanden under en viss tidsperiod. De här stegen är bara ett exempel på hur du kan komma fram till felmeddelandet du är intresserad av. Du kan använda en egenskap som är tillgänglig för att leta efter felen som du vill hitta.

1. Öppna OMS-arbetsytan som är kopplad till ditt HDInsight-kluster från Azure-portalen.
2. Välj den **Loggsökning** panelen.
3. Skriv följande fråga för att söka efter alla felmeddelanden för alla HDInsight-kluster som är konfigurerad för att använda Azure Log Analytics och välj sedan **kör**. 

         search "Error"

    Du bör se utdata som liknar följande utdata:

    ![Sök alla utdata för fel](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Sök alla utdata för fel")

4. I den vänstra rutan under **typ** kategori, Välj en typ som du vill gå djupare i och välj sedan **tillämpa**.  Observera resultatet är att finjusteras för att endast visa fel av typen som du har valt.
5. Du kan gå på djupet i fellistan för specifika med hjälp av alternativen som finns i den vänstra rutan. Exempel:

    - Visa felmeddelanden från en specifik arbetsnod:

        ![Sök efter specifika fel med utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Sök efter specifika fel med utdata")

    - Visa ett fel uppstod vid en viss tidpunkt:

        ![Sök efter specifika fel med utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Sök efter specifika fel med utdata")

6. För att se det specifika felet. Du kan välja **[+] Visa fler** att titta på det verkliga felmeddelandet.

    ![Sök efter specifika fel med utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Sök efter specifika fel med utdata")

## <a name="create-alerts-for-tracking-events"></a>Skapa aviseringar för spårning av händelser

Det första steget att skapa en avisering är att komma till en fråga baserad på aviseringen har utlösts. Du kan använda alla frågor som du vill skapa en avisering.

1. Öppna Log Analytics-arbetsytan som är kopplad till ditt HDInsight-kluster från Azure-portalen.
2. Välj den **Loggsökning** panelen.
3. Kör följande fråga där du vill skapa en avisering och välj sedan **kör**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Frågan innehåller en lista över misslyckade program som körs på HDInsight-kluster.

4. Välj **ny Aviseringsregel** överst på sidan.

    ![Ange fråga för att skapa en avisering](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "RETUR-fråga för att skapa en avisering")

5. I den **skapa regeln** fönstret anger du frågan och annan information för att skapa en avisering och välj sedan **skapa varningsregel**.

    ![Ange fråga för att skapa en avisering](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "RETUR-fråga för att skapa en avisering")

Redigera eller ta bort en befintlig avisering:

1. Öppna Log Analytics-arbetsytan från Azure-portalen.
2. I den vänstra menyn, Välj **avisering**.
3. Välj den avisering du vill redigera eller ta bort.
4. Du har följande alternativ: **spara**, **Ignorera**, **inaktivera**, och **ta bort**.

    ![HDInsight Log Analytics OMS borttagning av redigera](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Mer information finns i [erfarenhet av Varningsregler i Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Se också

* [Arbeta med Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Skapa Varningsregler i Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
