---
title: Använd Azure Monitor loggar för att övervaka Azure HDInsight-kluster
description: Lär dig hur du använder Azure Monitor loggar för att övervaka jobb som körs i ett HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162794"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Använd Azure Monitor loggar för att övervaka HDInsight-kluster

Lär dig hur du aktiverar Azure Monitor loggar för att övervaka Hadoop-kluster åtgärder i HDInsight och hur du lägger till en HDInsight-övervaknings lösning.

[Azure Monitor loggar](../log-analytics/log-analytics-overview.md) är en tjänst i Azure Monitor som övervakar molnet och lokala miljöer för att bibehålla deras tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* En Log Analytics-arbetsyta. Du kan tänka på den här arbets ytan som en unik Azure Monitor loggar miljö med ett eget data lager, data källor och lösningar. Anvisningar finns i [skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Ett Azure HDInsight-kluster. För närvarande kan du använda Azure Monitor loggar med följande HDInsight-kluster typer:

  * Hadoop
  * HBase
  * Interaktiv fråga
  * Kafka
  * Spark
  * Storm

  Instruktioner för hur du skapar ett HDInsight-kluster finns i [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Azure PowerShell AZ-modul.  Se [Introduktion till new Azure PowerShell AZ-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Se till att du har den senaste versionen. Om det behövs kör du `Update-Module -Name Az`.

> [!NOTE]  
> Vi rekommenderar att placera både HDInsight-klustret och Log Analytics-arbetsytan i samma region för bättre prestanda. Azure Monitor-loggar är inte tillgängliga i alla Azure-regioner.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Aktivera Azure Monitor loggar med hjälp av portalen

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster om du vill använda en Azure Log Analytics-arbetsyta för att övervaka jobb, felsökningsloggar osv.

1. Välj ditt kluster från [Azure Portal](https://portal.azure.com/).  Se [lista och Visa kluster](./hdinsight-administer-use-portal-linux.md#showClusters) för instruktioner. Klustret öppnas på en ny Portal sida.

1. Gå till vänster och välj **Azure Monitor**under **övervakning**.

1. I huvudvyn väljer du **Aktivera**under **Azure Monitor-integration**.

1. I list rutan **Välj en arbets yta** väljer du en befintlig Log Analytics-arbetsyta.

1. Välj **Spara**.  Det tar en stund att spara inställningen.

    ![Aktivera övervakning för HDInsight-kluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Aktivera övervakning för HDInsight-kluster")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Aktivera Azure Monitor loggar med Azure PowerShell

Du kan aktivera Azure Monitor loggar med hjälp av cmdleten [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) i Azure PowerShell AZ-modulen.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Om du vill inaktivera använder du cmdleten [disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
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

Anvisningar om hur du installerar en hanterings lösning finns [i hanterings lösningar i Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Du kan experimentera genom att installera en HDInsight Hadoop-övervaknings lösning. När den är färdig visas en **HDInsightHadoop** -panel under **Sammanfattning**. Välj panelen **HDInsightHadoop** . Det ser ut som HDInsightHadoop lösningen:

![HDInsight-lösningen för övervakningsvyn](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Rapporten visar inte några aktiviteter eftersom klustret är ett helt nytt kluster.

## <a name="configuring-performance-counters"></a>Konfigurera prestanda räknare

Azure Monitor stöder också insamling och analys av prestanda mått för noderna i klustret. Mer information om hur du aktiverar och konfigurerar den här funktionen finns i [data källor för Linux-prestanda i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Kluster granskning

HDInsight support kluster granskning med Azure Monitor loggar genom att importera följande typer av loggar:

* `log_gateway_audit_CL` – den här tabellen innehåller gransknings loggar från klusternoder som visar lyckade och misslyckade inloggnings försök.
* `log_auth_CL` – den här tabellen innehåller SSH-loggar med lyckade och misslyckade inloggnings försök.
* `log_ambari_audit_CL` – den här tabellen innehåller gransknings loggar från Ambari.
* `log_ranger_audti_CL` – den här tabellen innehåller gransknings loggar från Apache Ranger på ESP-kluster.

## <a name="next-steps"></a>Nästa steg

* [Fråga Azure Monitor loggar för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
