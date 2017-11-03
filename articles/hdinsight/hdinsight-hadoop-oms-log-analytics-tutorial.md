---
title: "Använda Log Analytics för att övervaka Azure HDInsight-kluster | Microsoft Docs"
description: "Lär dig hur du använder Azure Log Analytics för att övervaka jobb som körs i ett HDInsight-kluster."
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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: c1c74c797449c2fa6d76438f9ec33eaf0fe51af4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Använda Azure Log Analytics för att övervaka HDInsight-kluster (förhandsgranskning)

Lär dig hur du använder Azure Log Analytics för att övervaka åtgärder för HDInsight Hadoop-kluster i den här artikeln.

Log Analytics är en tjänst i [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) som övervakar molnet och lokala miljöer för att upprätthålla sin tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor. 

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [Skapa ett kostnadsfritt Azure-konto i dag](https://azure.microsoft.com/free).

* **Ett Azure HDInsight-kluster**. För närvarande kan du använda Azure OMS med följande typer av HDInsight-kluster:
    * Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Interaktiva Hive

    Instruktioner om hur du skapar ett HDInsight-kluster finns i [komma igång med Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).


* **Logganalys-arbetsytan**. Du kan se den här arbetsytan som en unik logganalys-miljö med en egen lagringsplats för data, datakällor och lösningar. Du måste ha en sådan arbetsyta som redan har skapats som du kan associera med Azure HDInsight-kluster. Instruktioner finns i [skapa logganalys-arbetsytan](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>Konfigurera HDInsight-kluster för att använda Azure logganalys

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster om du vill använda en Azure logganalys-arbetsyta för att övervaka jobb, felsökningsloggar osv.

1. I Azure-portalen från den vänstra rutan klickar du på **HDInsight-kluster**, och klicka sedan på namnet på det kluster som du vill konfigurera med Azure logganalys.

2. I bladet klustret i den vänstra rutan klickar du på **övervakning**.

3. I den högra rutan, klickar du på **aktivera**, och välj sedan en befintlig logganalys-arbetsyta. Klicka på **Spara**.

    ![Aktivera övervakning av HDInsight-kluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "aktivera övervakning av HDInsight-kluster")

4. När klustret har konfigurerats för att använda Log Analytics för övervakning kan du se en **öppna OMS-instrumentpanelen** alternativet längst upp på fliken. Klicka på knappen.

    ![Öppna instrumentpanelen i OMS](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "öppna OMS-instrumentpanelen")

5. Ange dina autentiseringsuppgifter för Azure om du uppmanas till detta. Du dirigeras till Microsoft OMS-instrumentpanelen.

    ![Operations Management Suite-portalen](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite-portalen")

## <a name="next-steps"></a>Nästa steg
* [Lägg till lösningar för hantering av HDInsight-klustret till logganalys](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
