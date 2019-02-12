---
title: Använda Log Analytics för att övervaka Azure HDInsight-kluster
description: Lär dig hur du använder Azure Log Analytics för att övervaka jobb som körs i ett HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cd129ea68315223516ac1cd3e7577b5ee4bf92e5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005122"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Använda Azure Log Analytics för att övervaka HDInsight-kluster

Lär dig hur du aktiverar Azure Log Analytics för att övervaka åtgärder för Hadoop-kluster i HDInsight och hur du lägger till ett Hdinsight övervakningslösning.

[Log Analytics](../log-analytics/log-analytics-overview.md) är en tjänst i Azure Monitor som övervakar dina molnbaserade och lokala miljöer för att bibehålla tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* **Log Analytics-arbetsytan**. Du kan betrakta arbetsytan som en unik Log Analytics-miljö med en egen databas, datakällor och lösningar. Anvisningar finns i [skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Ett Azure HDInsight-kluster**. För närvarande kan du använda Log Analytics med följande typer av HDInsight-kluster:

  * Hadoop
  * HBase
  * Interaktiv fråga
  * Kafka
  * Spark
  * Storm

  Anvisningar om hur du skapar ett HDInsight-kluster finns i [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!NOTE]  
> Vi rekommenderar att placera både HDInsight-klustret och Log Analytics-arbetsytan i samma region för bättre prestanda. Azure Log Analytics är inte tillgänglig i alla Azure-regioner.

## <a name="enable-log-analytics-by-using-the-portal"></a>Aktivera Log Analytics med hjälp av portalen

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster om du vill använda en Azure Log Analytics-arbetsyta för att övervaka jobb, felsökningsloggar osv.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra menyn, Välj **alla tjänster**.

3. Under **ANALYTICS**väljer **HDInsight-kluster**.

4. Från vänster, under **övervakning**väljer **Operations Management Suite**.

5. Från den huvudsakliga vyn under **OMS-övervakning**väljer **aktivera**.

6. Från den **Välj en arbetsyta** listrutan väljer du en befintlig Log Analytics-arbetsyta.

7. Välj **Spara**.

    ![Aktivera övervakning för HDInsight-kluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "aktiverar övervakning av HDInsight-kluster")

    Det tar en stund att spara inställningen.

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Aktivera Log Analytics med hjälp av Azure PowerShell

Du kan aktivera Log Analytics med hjälp av Azure PowerShell. Cmdlet: en är:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

Se [aktivera AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Om du vill inaktivera, är cmdleten:

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

Se [inaktivera AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).

## <a name="install-hdinsight-cluster-management-solutions"></a>Installera lösningar för hantering av HDInsight-kluster

HDInsight ger klusterspecifika hanteringslösningar som du kan lägga till för Azure Log Analytics. [Lösningar för hantering av](../log-analytics/log-analytics-add-solutions.md) lägga till funktioner i Log Analytics, tillhandahåller ytterligare data och analysverktyg. Dessa lösningar samla in viktiga prestandamått från dina HDInsight-kluster och innehåller verktyg för att söka efter mått. Dessas lösningar omfattar också visualiseringar och instrumentpaneler för de flesta klustertyper som stöds i HDInsight. Genom att använda de mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar.

Dessa är tillgängliga HDInsight-lösningar:

* HDInsight Hadoop-övervakning
* HDInsight HBase-övervakning
* Övervakning av interaktiv fråga i HDInsight
* HDInsight Kafka – övervakning
* HDInsight Spark – övervakning
* HDInsight Storm-övervakning

Anvisningar om hur du installerar en lösning finns i [lösningar i Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Du kan experimentera genom att installera en Monotiring för HDInsight Hadoop-lösning. När det är klart visas en **HDInsightHadoop** panelen visas under **sammanfattning**. Välj den **HDInsightHadoop** panelen. Det ser ut som HDInsightHadoop lösningen:

![HDInsight-lösningen för övervakningsvyn](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Rapporten visar inte några aktiviteter eftersom klustret är ett helt nytt kluster.

## <a name="next-steps"></a>Nästa steg

* [Fråga Azure Log Analytics för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
