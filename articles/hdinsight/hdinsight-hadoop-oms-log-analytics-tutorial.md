---
title: Använd Azure Monitor loggar för att övervaka Azure HDInsight-kluster
description: Lär dig hur du använder Azure Monitor loggar för att övervaka jobb som körs i ett HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: a693b14bb61eb52a09ab1f1ecd5d00b339357d5d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240376"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Använd Azure Monitor loggar för att övervaka HDInsight-kluster

Lär dig hur du aktiverar Azure Monitor loggar för att övervaka Hadoop-kluster åtgärder i HDInsight och hur du lägger till en HDInsight-övervaknings lösning.

[Azure Monitor loggar](../log-analytics/log-analytics-overview.md) är en tjänst i Azure Monitor som övervakar molnet och lokala miljöer för att bibehålla deras tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* **Log Analytics-arbetsytan**. Du kan tänka på den här arbets ytan som en unik Azure Monitor loggar miljö med ett eget data lager, data källor och lösningar. Anvisningar finns i [skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Ett Azure HDInsight-kluster**. För närvarande kan du använda Azure Monitor loggar med följande HDInsight-kluster typer:

  * Hadoop
  * HBase
  * Interaktiv fråga
  * Kafka
  * Spark
  * Storm

  Anvisningar om hur du skapar ett HDInsight-kluster finns i [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Azure PowerShell AZ-modul**.  Se [Introduktion till new Azure PowerShell AZ-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Vi rekommenderar att placera både HDInsight-klustret och Log Analytics-arbetsytan i samma region för bättre prestanda. Azure Monitor-loggar är inte tillgängliga i alla Azure-regioner.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Aktivera Azure Monitor loggar med hjälp av portalen

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster om du vill använda en Azure Log Analytics-arbetsyta för att övervaka jobb, felsökningsloggar osv.

1. Välj ditt kluster från [Azure Portal](https://portal.azure.com/).  Se [lista och Visa kluster](./hdinsight-administer-use-portal-linux.md#showClusters) för instruktioner. Klustret öppnas på en ny Portal sida.

1. Från vänster, under **övervakning**, väljer du **Operations Management Suite**.

1. Välj **Aktivera**i huvudvyn under **OMS-övervakning**.

1. I list rutan **Välj en arbets yta** väljer du en befintlig Log Analytics-arbetsyta.

1. Välj **Spara**.  Det tar en stund att spara inställningen.

    ![Aktivera övervakning för HDInsight-kluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "aktiverar övervakning av HDInsight-kluster")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Aktivera Azure Monitor loggar med Azure PowerShell

Du kan aktivera Azure Monitor loggar med hjälp av cmdleten [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) i Azure PowerShell AZ-modulen.

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

Om du vill inaktivera använder du cmdleten [disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) :

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installera lösningar för hantering av HDInsight-kluster

HDInsight tillhandahåller klusterbaserade hanterings lösningar som du kan lägga till för Azure Monitor loggar. [Hanterings lösningar](../log-analytics/log-analytics-add-solutions.md) lägger till funktioner i Azure Monitor loggar, vilket ger ytterligare data-och analys verktyg. Dessa lösningar samla in viktiga prestandamått från dina HDInsight-kluster och innehåller verktyg för att söka efter mått. Dessas lösningar omfattar också visualiseringar och instrumentpaneler för de flesta klustertyper som stöds i HDInsight. Genom att använda de mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar.

Dessa är tillgängliga HDInsight-lösningar:

* HDInsight Hadoop-övervakning
* HDInsight HBase-övervakning
* Övervakning av interaktiv fråga i HDInsight
* HDInsight Kafka – övervakning
* HDInsight Spark – övervakning
* HDInsight Storm-övervakning

Anvisningar om hur du installerar en lösning finns i [lösningar i Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Du kan experimentera genom att installera en HDInsight Hadoop-övervaknings lösning. När det är klart visas en **HDInsightHadoop** panelen visas under **sammanfattning**. Välj den **HDInsightHadoop** panelen. Det ser ut som HDInsightHadoop lösningen:

![HDInsight-lösningen för övervakningsvyn](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Rapporten visar inte några aktiviteter eftersom klustret är ett helt nytt kluster.

## <a name="configuring-performance-counters"></a>Konfigurera prestanda räknare

Azure Monitor stöder också insamling och analys av prestanda mått för noderna i klustret. Mer information om hur du aktiverar och konfigurerar den här funktionen finns i [data källor för Linux-prestanda i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Kluster granskning

HDInsight support kluster granskning med Azure Monitor loggar genom att importera följande typer av loggar:

* `log_gateway_audit_CL`– den här tabellen innehåller gransknings loggar från klusternoder som visar lyckade och misslyckade inloggnings försök.
* `log_auth_CL`– den här tabellen innehåller SSH-loggar med lyckade och misslyckade inloggnings försök.
* `log_ambari_audit_CL`– den här tabellen innehåller gransknings loggar från Ambari.
* `log_ranger_audti_CL`– den här tabellen innehåller gransknings loggar från Apache Ranger på ESP-kluster.

## <a name="next-steps"></a>Nästa steg

* [Fråga Azure Monitor loggar för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
