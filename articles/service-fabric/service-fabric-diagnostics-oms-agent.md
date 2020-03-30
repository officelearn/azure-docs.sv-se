---
title: Prestandaövervakning med Azure Monitor-loggar
description: Lär dig hur du konfigurerar Log Analytics Agent för övervakning av behållare och prestandaräknare för dina Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: c3c1bf511f3313e7408d6ce90b73de60bd1309f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366753"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Prestandaövervakning med Azure Monitor-loggar

Den här artikeln beskriver stegen för att lägga till Log Analytics-agenten som ett tillägg för skalningsuppsättning för virtuella datorer i klustret och ansluta den till din befintliga Azure Log Analytics-arbetsyta. Detta gör det möjligt att samla in diagnostikdata om behållare, program och prestandaövervakning. Genom att lägga till det som ett tillägg till den virtuella datorn skala uppsättning resurs, Azure Resource Manager säkerställer att det blir installerat på varje nod, även när skalning klustret.

> [!NOTE]
> Den här artikeln förutsätter att du redan har konfigurerat en Azure Log Analytics-arbetsyta. Om du inte gör det går du över till [Konfigurera Azure Monitor-loggar](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Lägg till agenttillägget via Azure CLI

Det bästa sättet att lägga till Log Analytics-agenten i klustret är via API:erna för den virtuella datorns skalningsuppsättning som är tillgängliga med Azure CLI. Om du inte har konfigurerat Azure CLI ännu, gå över till Azure-portalen och öppna en [Cloud Shell-instans](../cloud-shell/overview.md) eller [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. När ditt Cloud Shell har begärts kontrollerar du att du arbetar i samma prenumeration som din resurs. Kontrollera detta `az account show` med och se till att värdet "namn" matchar värdet för klustrets prenumeration.

2. I portalen navigerar du till resursgruppen där logganalysarbetsytan finns. Klicka på logganalysresursen (resurstypen kommer att vara Log Analytics-arbetsytan). När du är på sidan resursöversikt klickar du på **Avancerade inställningar** under avsnittet Inställningar på den vänstra menyn.

    ![Sidan Egenskaper för logganalys](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Klicka på **Windows-servrar** om du står upp ett Windows-kluster och **Linux-servrar** om du skapar ett Linux-kluster. På den här `workspace ID` sidan `workspace key` visas din och (listad som primärnyckel i portalen). Du behöver båda för nästa steg.

4. Kör kommandot för att installera Log Analytics-agenten i klustret med hjälp av API:et: `vmss extension set`

    För ett Windows-kluster:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    För ett Linux-kluster:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Här är ett exempel på att Log Analytics-agenten läggs till i ett Windows-kluster.

    ![Kommandot Log Analytics-agent cli](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Detta bör ta mindre än 15 min att framgångsrikt lägga till agenten till dina noder. Du kan kontrollera att agenterna har `az vmss extension list` lagts till med hjälp av API:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Lägga till agenten via Resource Manager-mallen

Exempel på Resource Manager-mallar som distribuerar en Azure Log Analytics-arbetsyta och lägger till en agent i var och en av dina noder är tillgänglig för [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) eller [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Du kan hämta och ändra den här mallen för att distribuera ett kluster som bäst passar dina behov.

## <a name="view-performance-counters"></a>Visa prestandaräknare

Nu när du har lagt till Log Analytics-agenten går du vidare till Log Analytics-portalen för att välja vilka prestandaräknare du vill samla in.

1. Gå till resursgruppen där du skapade Service Fabric Analytics-lösningen i Azure-portalen. Välj **ServiceFabric-namnAvLog\<\>AnalyticsWorkspace**.

2. Klicka på **Log Analytics**.

3. Klicka på **Avancerade inställningar**.

4. Klicka på **Data**och sedan på **Windows eller Linux-prestandaräknare**. Det finns en lista över standardräknare som du kan välja att aktivera och du kan också ange intervallet för samlingen. Du kan också lägga till [ytterligare prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) att samla in. Rätt format refereras i den här [artikeln](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Klicka på **Spara**och sedan på **OK**.

6. Stäng bladet Avancerade inställningar.

7. Klicka på Sammanfattning **av arbetsyta**under rubriken Allmänt.

8. Du kommer att se brickor i form av ett diagram för var och en av de lösningar som är aktiverade, inklusive en för Service Fabric. Klicka på diagrammet **Service Fabric** om du vill fortsätta till Service Fabric Analytics-lösningen.

9. Du kommer att se några paneler med grafer på operativ kanal och tillförlitliga tjänster händelser. Den grafiska representationen av data som flödar in för de räknare som du har valt visas under Nod metrics.

10. Klicka på ett containermåttdiagram för att se ytterligare information. Du kan också fråga på prestandaräknaredata på samma sätt som klusterhändelser och filtrera på noderna, perf-räknarens namn och värden med hjälp av Frågespråket Kusto.

![Logg analytics perf-räknare fråga](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Nästa steg

* Samla in relevanta [prestandaräknare](service-fabric-diagnostics-event-generation-perf.md). Om du vill konfigurera Log Analytics-agenten för att samla in specifika prestandaräknare granskar du [konfigurera datakällor](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Konfigurera Azure Monitor-loggar för att konfigurera [automatisk avisering](../log-analytics/log-analytics-alerts.md) för att underlätta identifiering och diagnostik
* Som ett alternativ kan du samla in prestandaräknare via [Azure Diagnostics-tillägget och skicka dem till Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
