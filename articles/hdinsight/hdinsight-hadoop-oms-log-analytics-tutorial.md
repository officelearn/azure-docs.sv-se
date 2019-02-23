---
title: Använda Log Analytics för att övervaka Azure HDInsight-kluster
description: Lär dig hur du använder Azure Log Analytics för att övervaka jobb som körs i ett HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: 5fe1aee35f5501d3ec4910aadb9ef43d2f9fb8ed
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727527"
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

* **Azure PowerShell Az-modulen**.  Se [introduktion till den nya Az för Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Vi rekommenderar att placera både HDInsight-klustret och Log Analytics-arbetsytan i samma region för bättre prestanda. Azure Log Analytics är inte tillgänglig i alla Azure-regioner.

## <a name="enable-log-analytics-by-using-the-portal"></a>Aktivera Log Analytics med hjälp av portalen

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster om du vill använda en Azure Log Analytics-arbetsyta för att övervaka jobb, felsökningsloggar osv.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra menyn, Välj **alla tjänster**.

3. Under **ANALYTICS**väljer **HDInsight-kluster**.

4. Välj ditt kluster i listan.

5. Från vänster, under **övervakning**väljer **Operations Management Suite**.

6. Från den huvudsakliga vyn under **OMS-övervakning**väljer **aktivera**.

7. Från den **Välj en arbetsyta** listrutan väljer du en befintlig Log Analytics-arbetsyta.

8. Välj **Spara**.  Det tar en stund att spara inställningen.

    ![Aktivera övervakning för HDInsight-kluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "aktiverar övervakning av HDInsight-kluster")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Aktivera Log Analytics med hjälp av Azure PowerShell

Du kan aktivera Log Analytics med hjälp av Azure PowerShell Az-modulen [aktivera AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) cmdlet.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Inaktivera användning den [inaktivera AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) cmdlet:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installera lösningar för hantering av HDInsight-kluster

HDInsight ger klusterspecifika hanteringslösningar som du kan lägga till för Azure Log Analytics. [Lösningar för hantering av](../log-analytics/log-analytics-add-solutions.md) lägga till funktioner i Log Analytics, tillhandahåller ytterligare data och analysverktyg. Dessa lösningar samla in viktiga prestandamått från dina HDInsight-kluster och innehåller verktyg för att söka efter mått. Dessas lösningar omfattar också visualiseringar och instrumentpaneler för de flesta klustertyper som stöds i HDInsight. Genom att använda de mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar.

Dessa är tillgängliga HDInsight-lösningar:

* HDInsight Hadoop-övervakning
* HDInsight HBase-övervakning
* Övervakning av interaktiv fråga i HDInsight
* HDInsight Kafka – övervakning
* HDInsight Spark – övervakning
* HDInsight Storm-övervakning

Anvisningar om hur du installerar en lösning finns i [lösningar i Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Om du vill experimentera, installera en lösning för övervakning av Hadoop i HDInsight. När det är klart visas en **HDInsightHadoop** panelen visas under **sammanfattning**. Välj den **HDInsightHadoop** panelen. Det ser ut som HDInsightHadoop lösningen:

![HDInsight-lösningen för övervakningsvyn](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Rapporten visar inte några aktiviteter eftersom klustret är ett helt nytt kluster.

## <a name="next-steps"></a>Nästa steg

* [Fråga Azure Log Analytics för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
