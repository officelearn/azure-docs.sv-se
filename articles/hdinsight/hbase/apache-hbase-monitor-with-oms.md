---
title: Övervaka HBase med Azure logganalys - Azure HDInsight | Microsoft Docs
description: Övervaka HDInsight HBase-kluster med hjälp av Azure logganalys.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 12ec60049cdf267834d251c6c927b35e3c363a4e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165306"
---
# <a name="monitor-hbase-with-log-analytics"></a>Övervakaren HBase med logganalys

Övervakning av HBase i HDInsight använder Azure Log Analytics att samla in prestandavärden för HDInsight HBase från HDInsight-klusternoder. Övervakaren ger HBase-specifika visualiseringar och instrumentpaneler, verktyg för att söka efter mätvärdena och möjligheten att skapa anpassade regler för övervakning och aviseringar. Du kan övervaka mätvärden för flera kluster i HDInsight HBase över flera Azure-prenumerationer.

Log Analytics är en tjänst i [Azure](../../operations-management-suite/operations-management-suite-overview.md) som övervakar molnet och lokala miljöer för att upprätthålla sin tillgänglighet och prestanda. Logganalys samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analys över flera källor.

[Logga Analytics hanteringslösningar](../../log-analytics/log-analytics-add-solutions.md) lägga till funktioner till logganalys ger ytterligare information och verktyg för analys. Hanteringslösningar för log Analytics är en samling logik och visualisering data förvärv regler som ger mått för ett visst område. En lösning kan också definiera nya posttyper för att samla in och dessa poster kan analyseras med loggen sökningar eller nya gränssnittsfunktioner.

[Insight & Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/) bygger på logganalys-plattform. Du kan välja att använda Log Analytics-funktionerna och betala per GB som inhämtas i tjänsten, eller växla arbetsytan Insight & Analytics-nivån och betala per nod som hanteras av tjänsten. Insight & Analytics erbjuder en supermängd funktionerna som erbjuds av logganalys. Övervakning av HBase-lösning är tillgänglig med logganalys eller Insight & Analytics.

När du etablerar en lösning för övervakning av HBase i HDInsight kan du skapa en logganalys-arbetsytan. Varje arbetsyta som en unik logganalys-miljö med en egen lagringsplats för data, datakällor och lösningar. Du kan skapa flera arbetsytor i din prenumeration för att stödja flera miljöer, till exempel produktion och testa.

## <a name="provision-hdinsight-hbase-monitoring"></a>Etablera HDInsight HBase övervakning

1. Logga in på den [Azure-portalen](https://portal.azure.com) med din Azure-prenumeration.
2. I den **ny** rutan under **Marketplace**väljer **övervakning + management**.
3. I den **övervakning + management** väljer **se alla**.

    ![Övervaka + hanteringsfönstret](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. I listan, leta efter den **hanteringslösningar** band. Till höger om **hanteringslösningar**väljer **mer**.

    ![Övervaka + hanteringsfönstret](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. I den **hanteringslösningar** fönstret Välj hanteringslösning HDInsight HBase övervakning för att lägga till i en arbetsyta.

    ![Hantering av lösningar fönstret](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. Granska informationen om hanteringslösningen i fönstret management-lösning och välj sedan **skapa**. 
7. I den *management lösningsnamn* väljer en befintlig arbetsyta associera med hanteringslösningen eller skapa en ny logganalys-arbetsyta och markerar den.
8. Ändra arbetsytan inställningar för Azure-prenumeration, resursgrupp och plats efter behov. 
    ![lösningens arbetsyta](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Välj **Skapa**.  
10. Om du vill använda denna lösning för hantering i arbetsytan, gå till **logganalys** > ***Arbetsytenamn*** > **lösningar**. En post för din lösning visas i listan. Välj posten att navigera till lösningen.

    ![Loggen Analyslösningar](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. Fönstret för din HDInsight HBase övervakningslösning visas.

    ![HDInsight HBase lösningar fönstret](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Översikt över paneler visas inte några data eftersom du ännu inte har konfigurerat dina HDInsight HBase-kluster för att skicka data till logganalys.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Ansluta HDInsight HBase-kluster till logganalys

Om du vill använda de verktyg som tillhandahålls av HDInsight HBase övervakning, måste du konfigurera klustret så att den skickar måtten från dess region server, huvudnoderna och ZooKeeper-noder till logganalys. Den här konfigurationen görs genom att köra en skriptåtgärd mot HDInsight HBase-kluster.

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>Hämta Log Analytics arbetsyte-ID och Arbetsytenyckel

Du behöver din Log Analytics arbetsyte-ID och Arbetsytenyckel för att noder i klustret för att autentisera med logganalys. Hämta dessa värden:

1. Välj översikt över din HBase övervakning rutan i Azure-portalen.

    ![Övervakning av HBase-lösningen fönstret](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Välj OMS-portalen för att starta OMS-portalen i en ny webbläsarflik eller i fönstret.

    ![Välj OMS-portalen](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. Välj inställningar på OMS-portalen hemma.

    ![OMS-portal](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Välj ansluten källa, Linux-servrar.

    ![Välj anslutna käll- och Linux-servrar](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Observera värdena arbetsyte-ID och primärnyckel visas, eftersom detta är de värden som du behöver för skriptåtgärden.

### <a name="run-the-script-action"></a>Köra instruktionen skript

Om du vill aktivera insamling av data från HDInsight HBase-kluster, kör du en skriptåtgärd mot alla noder i klustret:

1. Gå till fönstret för HDInsight HBase-kluster i Azure-portalen.
2. Välj **skript åtgärder**.

    ![Skriptåtgärder](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Välj **skicka nya**.

    ![Skicka nya](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. I den **skicka skript** , inställd skripttypen på `"- Custom"`.
5. Ange ett namn för det här skriptet.
6. För den **Bash skript URI**, klistra in följande URI: N:

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. För den **nodtyper**, väljer du alla tre (**Head**, **Region**, **ZooKeeper**).
8. I den **parametrar** textrutan Ange arbetsyte-ID och din Arbetsytenyckel, omslutande varje värde i citattecken och avgränsat med ett blanksteg.

        "<Workspace ID>" "<Workspace Key>"

9. Välj **spara den här skriptåtgärden** att köra åtgärden när nya noder läggs till i klustret.

    ![Åtgärden skriptinställningar](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Välj **Skapa**.
11. Skriptåtgärden tar några minuter att köra. Du kan övervaka statusen i åtgärdsfönstret i skriptet.

    ![Skriptåtgärder körs](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. När skriptåtgärden är klar visas en grön bockmarkering bredvid skriptnamnet på i listan.

    ![Skriptåtgärden har slutförts](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>Visa övervakning av HDInsight-HBase-lösning

När skriptåtgärden är klar bör du se data i övervakning-lösningen inom några minuter.

1. Navigera till HDInsight HBase-lösningens rutan i Azure-portalen.
2. Välj den **översikt** fliken.
3. Under **sammanfattning**, visas en ikon som indikerar antalet Region servrar som övervakas.

    ![Övervakning av panelen Sammanfattning](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Välj ikonen med antalet region server. Huvudinstrumentpanelen visas.
5. Den här instrumentpanelen tillhandahåller åtkomst till statistik om regionerna, antalet skrivåtgärder-ahead-loggen (förnyelse) används, en samling logganalys sökningar (till exempel för region server loggar Phoenix loggar och undantag) och en mängd populära diagram visualisera relevanta mått. 

    ![Huvudinstrumentpanelen](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. Att välja något av dessa kommer detaljnivån i vyn loggen Sök kan du förfina frågan och utforska data i detalj.

## <a name="next-steps"></a>Nästa steg

* [Skapa aviseringar i logganalys](../../log-analytics/log-analytics-alerts-creating.md)
* [Söka efter data med loggen sökningar i Azure Log Analytics](../../log-analytics/log-analytics-log-searches.md).
