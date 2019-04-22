---
title: Använd Azure Monitor-loggar för att övervaka Azure HDInsight-kluster
description: Lär dig hur du använder Azure Azure Monitor-loggar för att övervaka jobb som körs i ett HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: 399a6bcb6e0bfd0edaddca471ba2c8e0802d3394
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904788"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Använd Azure Monitor-loggar för att övervaka HDInsight-kluster

Lär dig hur du aktiverar Azure Monitor-loggar att övervaka åtgärder för Hadoop-kluster i HDInsight och hur du lägger till ett HDInsight övervakningslösning.

[Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) är en tjänst i Azure Monitor som övervakar dina molnbaserade och lokala miljöer för att bibehålla tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* **Log Analytics-arbetsytan**. Du kan betrakta arbetsytan som en unik Azure Monitor-loggar miljö med en egen databas, datakällor och lösningar. Anvisningar finns i [skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Ett Azure HDInsight-kluster**. För närvarande kan du använda Azure Monitor-loggar med följande typer av HDInsight-kluster:

  * Hadoop
  * HBase
  * Interaktiv fråga
  * Kafka
  * Spark
  * Storm

  Anvisningar om hur du skapar ett HDInsight-kluster finns i [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Azure PowerShell Az-modulen**.  Se [introduktion till den nya Az för Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Vi rekommenderar att placera både HDInsight-klustret och Log Analytics-arbetsytan i samma region för bättre prestanda. Azure Monitor-loggar är inte tillgänglig i alla Azure-regioner.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Aktivera Azure Monitor-loggar med hjälp av portalen

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

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Aktivera Azure Monitor-loggar med hjälp av Azure PowerShell

Du kan aktivera Azure Monitor-loggar med Azure PowerShell Az-modulen [aktivera AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) cmdlet.

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

HDInsight ger klusterspecifika hanteringslösningar som du kan lägga till för Azure Monitor-loggar. [Lösningar för hantering av](../log-analytics/log-analytics-add-solutions.md) lägga till funktioner i Azure Monitor-loggar, tillhandahåller ytterligare data och analysverktyg. Dessa lösningar samla in viktiga prestandamått från dina HDInsight-kluster och innehåller verktyg för att söka efter mått. Dessas lösningar omfattar också visualiseringar och instrumentpaneler för de flesta klustertyper som stöds i HDInsight. Genom att använda de mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar.

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

* [Fråga Azure Monitor-loggar för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
