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
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 2c43b9aabda56e1204870575e86934bf93d05882
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Använda Azure Log Analytics för att övervaka HDInsight-kluster

Lär dig hur du använder Azure Log Analytics för att övervaka åtgärder för Hadoop-kluster i HDInsight.

[Logga Analytics](../log-analytics/log-analytics-overview.md) är en tjänst i [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) som övervakar molnet och lokala miljöer för att upprätthålla sin tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor. 

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [Skapa ett kostnadsfritt Azure-konto i dag](https://azure.microsoft.com/free).

* **Ett Azure HDInsight-kluster**. För närvarande kan du använda Azure Operations Management Suite med följande typer av HDInsight-kluster:

    * Hadoop
    * HBase
    * Interaktiv fråga
    * Kafka
    * Spark
    * Storm

    Instruktioner om hur du skapar ett HDInsight-kluster finns i [komma igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Logganalys-arbetsytan**. Du kan se den här arbetsytan som en unik logganalys-miljö med en egen lagringsplats för data, datakällor och lösningar. Du måste ha en sådan arbetsyta som redan har skapats som du kan associera med Azure HDInsight-kluster. Instruktioner finns i [skapa logganalys-arbetsytan](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="enable-log-analytics-by-using-the-portal"></a>Aktivera Log Analytics med hjälp av portalen

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster om du vill använda en Azure logganalys-arbetsyta för att övervaka jobb, felsökningsloggar osv.

1. Öppna ett HDInsight-kluster i Azure-portalen.
2. I den vänstra rutan klickar du på **övervakning**.
3. I den högra rutan, klickar du på **aktivera**, Välj en befintlig logganalys-arbetsyta och klicka sedan på **spara**.

    ![Aktivera övervakning av HDInsight-kluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "aktivera övervakning av HDInsight-kluster")

    Det tar en stund att spara inställningen.  När det är klart kan du se en **öppna OMS-instrumentpanelen** knappen längst upp. 

    ![Öppna Operations Management Suite-instrumentpanelen](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "öppna OMS-instrumentpanelen")

5. Klicka på **öppna OMS instrumentpanelen**.
6. Ange dina autentiseringsuppgifter för Azure om du uppmanas till detta.

    ![Operations Management Suite-portalen](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite-portalen")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Aktivera Log Analytics med hjälp av Azure PowerShell

Du kan aktivera Log Analytics med hjälp av Azure PowerShell. Cmdlet är:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

Se [aktivera AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Om du vill inaktivera, är cmdlet 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

Se [inaktivera AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).


## <a name="next-steps"></a>Nästa steg
* [Lägg till lösningar för hantering av HDInsight-klustret till logganalys](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
