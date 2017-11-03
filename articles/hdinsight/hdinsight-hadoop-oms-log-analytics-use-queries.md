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
ms.date: 09/11/2017
ms.author: nitinme
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Frågan Azure Log Analytics för att övervaka HDInsight-kluster (förhandsgranskning)

I den här artikeln får titta du på vissa scenarier för hur du använder Azure logganalys med Azure HDInsight-kluster. Tre vanligaste scenarierna är:

* Analysera HDInsight-kluster mått i OMS
* Sök efter specifika loggmeddelanden för HDInsight-kluster
* Skapa aviseringar baserat på händelser som inträffar i kluster

## <a name="prerequisites"></a>Krav

* Du måste ha konfigurerat ett HDInsight-kluster för att använda Azure logganalys. Instruktioner finns i [Använd Azure logganalys med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Du måste ha lagt till HDInsight klusterspecifika hanteringslösningar till OMS-arbetsyta som beskrivs i [lägga till HDInsight-kluster hanteringslösningar till logganalys](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>Analysera HDInsight-kluster mått i OMS

I det här avsnittet ska gå vi igenom stegen för att leta efter specifika mått för HDInsight-kluster.

1. Öppna OMS-instrumentpanelen. Öppna bladet HDInsight-kluster som du har associerat med Azure logganalys i Azure-portalen, klicka på fliken övervakning, och klicka på **öppna OMS-instrumentpanelen**.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

2. I OMS-instrumentpanelen från startsidan, klickar du på **loggen Sök**.

    ![Öppna loggen sökning](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "öppna loggen sökning")

3. I fönstret loggen i den **Begin Sök här** skriver `*` att söka efter alla mätvärden för alla tillgängliga mått för alla HDInsight-kluster som är konfigurerad för att använda Azure logganalys. Tryck på RETUR.

    ![Sök alla](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Sök alla mått")

4. Du bör se utdata som liknar följande.

    ![Sök alla mått utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Sök alla mått-utdata")

5. I den vänstra rutan under **typen** kategori, Sök ett mått som du vill gräva djupare i. För den här kursen ska vi välja `metrics_resourcemanager_queue_root_default_CL`. Markera kryssrutan som motsvarar måttet och klicka sedan på **tillämpa**.

    > [!NOTE]
    > Du kan behöva klicka på den **[+] mer** för att hitta måttet som du letar efter. Dessutom den **tillämpa** är knappen längst ned i listan så du måste rulla nedåt för att se den.
    > 
    >    
    Observera att frågan i textrutan nu ändras till visas i rutan markerade i följande skärmbild:

    ![Sök efter specifika mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Sök efter specifika mått")

6. Du kan nu gräva djupare i den här specifika mått. Du kan nu begränsa att befintliga utdata baserat på medelvärdet av de resurser som används i en 10 minuters intervall, kategoriserade efter klusternamnet. Skriv följande fråga i textrutan fråga.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![Sök efter specifika mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "Sök efter specifika mått")

7. I stället för att förfina baserat på medelvärdet av de resurser som används, kan du använda följande fråga för att förfina resultatet baserat på när de maximala användes (samt 90 och 95: e percentilen) i ett fönster i 10 minuter.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![Sök efter specifika mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "Sök efter specifika mått")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>Sök efter specifika loggmeddelanden i HDInsight-kluster

I det här avsnittet gå vi igenom stegen för att leta felmeddelanden under en viss tid. Stegen här är bara ett exempel på hur du kan komma fram till ett felmeddelande du är intresserad av. Du kan använda en egenskap som är tillgängligt för att leta efter fel som du försöker hitta.

1. Öppna OMS-instrumentpanelen. Öppna bladet HDInsight-kluster som du har associerat med Azure logganalys i Azure-portalen, klicka på fliken övervakning, och klicka på **öppna OMS-instrumentpanelen**.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

2. I OMS-instrumentpanelen från startsidan, klickar du på **loggen Sök**.

    ![Öppna loggen sökning](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "öppna loggen sökning")

3. I fönstret loggen i den **Begin Sök här** skriver `"Error"` (med citationstecken) att söka efter alla felmeddelanden för alla HDInsight-kluster som är konfigurerad för att använda Azure logganalys. Tryck på RETUR.

    ![Sök alla fel](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "Sök alla fel")

4. Du bör se utdata som liknar följande.

    ![Sök alla fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Sök alla fel-utdata")

5. I den vänstra rutan under **typen** kategori, söka efter en typ som du vill gräva djupare i. För den här kursen ska vi välja `log_sparkappsexecutors_CL`. Markera kryssrutan som motsvarar måttet och klicka sedan på **tillämpa**.

    ![Sök efter specifika fel](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "Sök efter specifika fel")

        
6. Observera att frågan i textrutan ändras nu till i den markerade rutan nedan och resultatet är förfinade bara visar fel av typen du valt.

    ![Sök efter specifika fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "Sök efter specifika fel utdata")

7. Du kan nu gräva djupare i specifika felet listan med hjälp av alternativen i den vänstra rutan. Exempelvis kan du förfina frågan ska bara titta på felmeddelanden från en specifik arbetsnoden.

    ![Sök efter specifika fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Sök efter specifika fel utdata")

8. Du kan ytterligare zonen på den tid som du tror att felet uppstod genom att välja relevanta tidsvärdet i den vänstra rutan.

    ![Sök efter specifika fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Sök efter specifika fel utdata")

9. Du är nu till det specifika felet som du letar efter. Du kan klicka på **[+] Visa fler** titta på det verkliga felmeddelandet.

    ![Sök efter specifika fel utdata](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Sök efter specifika fel utdata")

## <a name="create-alerts-to-track-events"></a>Skapa aviseringar om du vill spåra händelser

Det första steget att skapa en avisering är att få fram en fråga baserat på som en avisering utlöses. För enkelhetens skull skriver du följande fråga som innehåller listan över misslyckade program som körs på HDInsight-kluster.

    * (Typ = metrics_resourcemanager_queue_root_default_CL) AppsFailed_d > 0 

Du kan använda en fråga som du vill skapa en avisering.

1. Öppna OMS-instrumentpanelen. Öppna bladet HDInsight-kluster som du har associerat med Azure logganalys i Azure-portalen, klicka på fliken övervakning, och klicka på **öppna OMS-instrumentpanelen**.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "öppna OMS-instrumentpanelen")

2. I OMS-instrumentpanelen från startsidan, klickar du på **loggen Sök**.

    ![Öppna loggen sökning](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "öppna loggen sökning")

3. I fönstret loggen i den **Begin Sök här** text klistra in frågan som du vill skapa en avisering, tryck på RETUR och klicka sedan på den **avisering** knappen.

    ![Ange frågan för att skapa en avisering](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "RETUR frågan för att skapa en avisering")

4. I den **Lägg till regel varning** fönstret Ange frågan och annan information för att skapa en avisering och klicka sedan på **spara**.

    ![Ange frågan för att skapa en avisering](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "RETUR frågan för att skapa en avisering")

    I den här skärmbilden skicka vi bara ett e-postmeddelande om avisering frågan hämtar utdata.

5. Du kan också redigera eller ta bort en befintlig avisering. Om du vill göra det, från valfri sida i OMS-portalen, klickar du på den **inställningar** ikon.

    ![Ange frågan för att skapa en avisering](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "RETUR frågan för att skapa en avisering")

6. Från den **inställningar** klickar du på **aviseringar** så visas aviseringarna som du har skapat. Du kan också aktivera eller inaktivera en avisering, redigera eller ta bort den. Mer information finns i [arbeta med Varningsregler i logganalys](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Se även

* [Arbeta med OMS logganalys](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Skapa Varningsregler i logganalys](../log-analytics/log-analytics-alerts-creating.md)
