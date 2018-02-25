---
title: "Fråga Azure Log Analytics för att övervaka Azure HDInsight-kluster | Microsoft Docs"
description: "Lär dig mer om att köra frågor i Azure Log Analytics för att övervaka jobb som körs i ett HDInsight-kluster."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: f9e023ae8d6c50049b588cf056629cd8d46ee603
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Frågan Azure Log Analytics för att övervaka HDInsight-kluster

Läs om några grundläggande scenarier för hur du använder Azure Log Analytics för att övervaka Azure HDInsight-kluster:

* [Analysera mått för HDInsight-kluster](#analyze-hdinsight-cluster-metrics)
* [Sök efter specifika loggmeddelanden](#search-for-specific-log-messages)
* [Skapa aviseringar](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha konfigurerat ett HDInsight-kluster för att använda Azure logganalys. Instruktioner finns i [Använd Azure logganalys med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Du måste ha lagt till HDInsight klusterspecifika hanteringslösningarna till den [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) arbetsytan enligt beskrivningen i [lägga till HDInsight-kluster hanteringslösningar till logganalys](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analysera mått för HDInsight-kluster

Lär dig mer om att söka efter specifika mått för HDInsight-kluster.

1. Öppna ett HDInsight-kluster som du har associerat med Azure logganalys i Azure-portalen.
2. Klicka på **övervakning**, och klicka sedan på **öppna OMS-instrumentpanelen**.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

2. Klicka på **loggen Sök** på den vänstra menyn.

    ![Öppna loggen sökning](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "öppna loggen sökning")

3. Skriv följande fråga i sökrutan för att söka efter alla mätvärden för alla tillgängliga mått för alla HDInsight-kluster som konfigurerats att använda Azure logganalys och tryck sedan på **RETUR**.

        `search *` 

    ![Sök alla](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Sök alla mått")

    Resultatet bör se ut:

    ![Sök alla mått utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Sök alla mått-utdata")

5. I den vänstra rutan under **typen**, markera ett mått som du vill gräva djupare i **tillämpa**. I följande skärmbild visas den `metrics_resourcemanager_queue_root_default_CL` typ har valts. 

    > [!NOTE]
    > Du kan behöva klicka på den **[+] mer** för att hitta måttet som du letar efter. Dessutom den **tillämpa** är knappen längst ned i listan så du måste rulla nedåt för att se den.
    > 
    >    

    Observera att frågan i textrutan ändras till visas i rutan markerade i följande skärmbild:

    ![Sök efter specifika mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Sök efter specifika mått")

6. Gräva djupare i den här specifika mått. Exempelvis kan du förfina befintliga utdata baserat på medelvärdet av de resurser som används i en 10 minuters intervall, kategoriserade efter klusternamnet med följande fråga:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. I stället för att förfina baserat på medelvärdet av de resurser som används, kan du använda följande fråga för att förfina resultatet baserat på när de maximala användes (samt 90 och 95: e percentilen) i ett fönster med 10 minuters:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Sök efter specifika loggmeddelanden

Lär dig hur man söker felmeddelanden under en viss tid. Stegen här är bara ett exempel på hur du kan komma fram till ett felmeddelande du är intresserad av. Du kan använda en egenskap som är tillgängligt för att leta efter fel som du försöker hitta.

1. Öppna ett HDInsight-kluster som du har associerat med Azure logganalys i Azure-portalen.
2. Klicka på **övervakning**, och klicka på **öppna OMS-instrumentpanelen**.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

2. I OMS-instrumentpanelen från startsidan, klickar du på **loggen Sök**.

    ![Öppna loggen sökning](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "öppna loggen sökning")

3. Skriv följande fråga för att söka efter alla felmeddelanden för alla HDInsight-kluster som är konfigurerad för att använda Azure logganalys och tryck sedan på **RETUR**. 

         search "Error"

    Du bör se utdata som liknar följande utdata:

    ![Sök alla fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Sök alla fel-utdata")

5. I den vänstra rutan under **typen** kategori, Välj en typ som du vill gräva djupare i och klicka sedan på **tillämpa**.  Observera att resultaten förfinad för att endast visa fel av typen du valt.
7. Du gräva djupare i den här listan för specifika felet med hjälp av alternativen i den vänstra rutan. Exempel: 

    - Visa felmeddelanden från en specifik arbetsnod:

        ![Sök efter specifika fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Sök efter specifika fel utdata")

    - Visa ett fel uppstod vid en viss tid:

        ![Sök efter specifika fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Sök efter specifika fel utdata")

9. För att se det specifika felet. Du kan klicka på **[+] Visa fler** titta på det verkliga felmeddelandet.

    ![Sök efter specifika fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Sök efter specifika fel utdata")

## <a name="create-alerts-for-tracking-events"></a>Skapa aviseringar om du vill spåra händelser

Det första steget att skapa en avisering är att få fram en fråga baserat på som en avisering utlöses. För enkelhetens skull skriver du följande fråga som innehåller listan över misslyckade program som körs på HDInsight-kluster.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

Du kan använda en fråga som du vill skapa en avisering.

1. Öppna ett HDInsight-kluster som du har associerat med Azure logganalys i Azure-portalen.
2. Klicka på **övervakning**, och klicka sedan på **öppna OMS-instrumentpanelen**.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

2. I OMS-instrumentpanelen från startsidan, klickar du på **loggen Sök**.

    ![Öppna loggen sökning](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "öppna loggen sökning")

3. Kör följande fråga där du vill skapa en avisering och tryck sedan på **RETUR**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Klicka på **avisering** överst på sidan.

    ![Ange frågan för att skapa en avisering](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "RETUR frågan för att skapa en avisering")

4. I den **Lägg till regel varning** fönstret Ange frågan och annan information för att skapa en avisering och klicka sedan på **spara**.

    ![Ange frågan för att skapa en avisering](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "RETUR frågan för att skapa en avisering")

    Skärmbilden visar konfigurationen för att skicka ett e-postmeddelande när aviseringen frågan returnerar utdata.

5. Du kan också redigera eller ta bort en befintlig avisering. Om du vill göra det, från valfri sida i OMS-portalen, klickar du på den **inställningar** ikon.

    ![Ange frågan för att skapa en avisering](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "RETUR frågan för att skapa en avisering")

6. Från den **inställningar** klickar du på **aviseringar** så visas aviseringarna som du har skapat. Du kan också aktivera eller inaktivera en avisering, redigera eller ta bort den. Mer information finns i [arbeta med Varningsregler i logganalys](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Se också

* [Arbeta med Operations Management Suite logganalys](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Skapa Varningsregler i logganalys](../log-analytics/log-analytics-alerts-creating.md)
