---
title: Använda Azure Monitor-loggar för att övervaka Azure HDInsight-kluster
description: Lär dig hur du använder Azure Monitor-loggar för att övervaka jobb som körs i ett HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162794"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Använda Azure Monitor-loggar för att övervaka HDInsight-kluster

Lär dig hur du aktiverar Azure Monitor-loggar för att övervaka Hadoop-klusteråtgärder i HDInsight och hur du lägger till en HDInsight-övervakningslösning.

[Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) är en tjänst i Azure Monitor som övervakar dina molnmiljöer och lokala miljöer för att behålla deras tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

* En Log Analytics-arbetsyta. Du kan se den här arbetsytan som en unik Azure Monitor-loggmiljö med en egen datalagringsplats, datakällor och lösningar. Instruktioner finns i [Skapa en log analytics-arbetsyta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Ett Azure HDInsight-kluster. För närvarande kan du använda Azure Monitor-loggar med följande HDInsight-klustertyper:

  * Hadoop
  * HBase
  * Interaktiv fråga
  * Kafka
  * Spark
  * Storm

  Instruktioner om hur du skapar ett HDInsight-kluster finns i [Komma igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Azure PowerShell Az-modul.  Se [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Se till att du har den senaste versionen. Om det `Update-Module -Name Az`behövs, kör .

> [!NOTE]  
> Vi rekommenderar att du placerar både HDInsight-klustret och log analytics-arbetsytan i samma region för bättre prestanda. Azure Monitor-loggar är inte tillgängliga i alla Azure-regioner.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Aktivera Azure Monitor-loggar med hjälp av portalen

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster för att använda en Azure Log Analytics-arbetsyta för att övervaka jobb, felsökloggar osv.

1. Välj ditt kluster i [Azure-portalen.](https://portal.azure.com/)  Se [Lista och visa kluster](./hdinsight-administer-use-portal-linux.md#showClusters) för instruktionerna. Klustret öppnas på en ny portalsida.

1. Välj **Azure Monitor**från vänster under **Övervakning**.

1. Välj **Aktivera**i huvudvyn under **Azure Monitor Integration**.

1. Välj en befintlig log Analytics-arbetsyta i listrutan **Välj en arbetsyta.**

1. Välj **Spara**.  Det tar en stund att spara inställningen.

    ![Aktivera övervakning för HDInsight-kluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Aktivera övervakning för HDInsight-kluster")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Aktivera Azure Monitor-loggar med Hjälp av Azure PowerShell

Du kan aktivera Azure Monitor-loggar med azure PowerShell [Az-modulen Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet.

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

För att inaktivera, använd [disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) cmdlet:

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installera lösningar för hantering av HDInsight-kluster

HDInsight tillhandahåller klusterspecifika hanteringslösningar som du kan lägga till för Azure Monitor-loggar. [Hanteringslösningar](../log-analytics/log-analytics-add-solutions.md) lägger till funktioner i Azure Monitor-loggar, vilket ger ytterligare data och analysverktyg. Dessa lösningar samlar in viktiga prestandamått från dina HDInsight-kluster och tillhandahåller verktygen för att söka i måtten. Dessa lösningar tillhandahåller även visualiseringar och instrumentpaneler för de flesta klustertyper som stöds i HDInsight. Genom att använda de mått som du samlar in med lösningen kan du skapa anpassade övervakningsregler och aviseringar.

Dessa är de tillgängliga HDInsight-lösningarna:

* HDInsight Hadoop Övervakning
* HDInsight HBase Övervakning
* HDInsight interaktiv frågeövervakning
* HDInsight Kafka Övervakning
* HDInsight Spark Övervakning
* HdInsight Storm Övervakning

Instruktioner för hur du installerar en hanteringslösning finns [i Hanteringslösningar i Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). För att experimentera installerar du en HDInsight Hadoop Monitoring-lösning. När det är klart ser du en **HDInsightHadoop-panel** som listas under **Sammanfattning**. Välj **HDInsightHadoop-panelen.** HDInsightHadoop-lösningen ser ut som:

![Lösningsvyn för HDInsight-övervakning](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Eftersom klustret är ett helt nytt kluster visar rapporten inga aktiviteter.

## <a name="configuring-performance-counters"></a>Konfigurera prestandaräknare

Azure-övervakaren stöder också insamling och analys av prestandamått för noderna i klustret. Mer information om hur du aktiverar och konfigurerar den här funktionen finns [i Linux prestandadatakällor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Klustergranskning

HDInsight stöder klustergranskning med Azure Monitor-loggar genom att importera följande typer av loggar:

* `log_gateway_audit_CL`- Den här tabellen innehåller granskningsloggar från klustergatewaynoder som visar lyckade och misslyckade inloggningsförsök.
* `log_auth_CL`- Den här tabellen ger SSH-loggar framgångsrikt och misslyckat inloggningsförsök.
* `log_ambari_audit_CL`- Den här tabellen innehåller granskningsloggar från Ambari.
* `log_ranger_audti_CL`- Den här tabellen innehåller granskningsloggar från Apache Ranger på ESP-kluster.

## <a name="next-steps"></a>Nästa steg

* [Fråga Azure Monitor-loggar för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
