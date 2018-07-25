---
title: Azure Service Fabric - prestandaövervakning med Log Analytics | Microsoft Docs
description: Lär dig hur du ställer in Log Analytics-agenten för övervakning av behållare och prestandaräknare för Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: b97a32e2e859a5bb370873bfbdc5c6b4dffa1ac1
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237850"
---
# <a name="performance-monitoring-with-log-analytics"></a>Prestandaövervakning med Log Analytics

Den här artikeln beskriver stegen för att lägga till Log Analytics-agenten som en VM-skalningsuppsättningen tillägg till klustret och ansluter den till din befintliga Azure Log Analytics-arbetsyta. På så sätt kan samla in diagnostikdata om behållare, program och prestandaövervakning. Genom att lägga till det som ett tillägg till VM scale set resursen, garanterar Azure Resource Manager att det installeras på varje nod, även när skala klustret.

> [!NOTE]
> Den här artikeln förutsätter att du har en Azure Log Analytics-arbetsyta som redan har konfigurerat. Om du inte vill gå till [ställa in Azure Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Lägg till agenttillägg via Azure CLI

Det bästa sättet att lägga till Log Analytics-agenten till ditt kluster anges via VM-skalningsuppsättningen API: er tillgängliga med Azure CLI. Om du inte har Azure CLI ännu, gå till Azure-portalen och öppna upp en [Cloud Shell](../cloud-shell/overview.md) instans, eller [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. När Cloud Shell har begärts, kontrollera att du arbetar i samma prenumeration som din resurs. Kontrollera detta med `az account show` och kontrollera att värdet för ”name” överensstämmer med ditt kluster-prenumeration.

2. Navigera till resursgruppen där Log Analytics-arbetsytan finns i portalen. Klicka i Log Analytics-resursen (typ av resursen är Log Analytics). När du är på översiktssidan för resursen klickar du på **avancerade inställningar** under avsnittet inställningar på den vänstra menyn.

    ![Log Analytics-egenskapssidan](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Klicka på **Windows-servrar** om du är att ställa upp ett Windows-kluster och **Linux-servrar** om du skapar ett Linux-kluster. Den här sidan visas dina `workspace ID` och `workspace key` (visas som primärnyckel i portalen). Du måste båda för nästa steg.

4. Kör kommandot för att installera Log Analytics-agenten på klustret med hjälp av den `vmss extension set` API i Cloud Shell:

    För ett Windows-kluster:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    För en Linux-kluster:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Här är ett exempel på Log Analytics-agenten som läggs till ett Windows-kluster.

    ![Logga in cli-kommando för Analytics-agenten](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Det bör ta mindre än 15 minuter att lägga till agenten till noderna. Du kan kontrollera att agenterna har lagts till med hjälp av den `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Lägga till agenten via Resource Manager-mall

Exemplet Resource Manager-mallar som distribuerar en Azure Log Analytics-arbetsyta och lägga till en agent i var och en av noderna är tillgänglig för [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) eller [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Du kan hämta och ändra den här mallen om du vill distribuera ett kluster som bäst passar dina behov.

## <a name="view-performance-counters"></a>Visa prestandaräknare

Nu när du har lagt till Log Analytics-agenten, head vill till Log Analytics-portalen för att välja vilka räknare som du samla in. 

1. Gå till resursgruppen där du skapade Service Fabric-analys-lösningen i Azure-portalen. Välj **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Klicka på **Log Analytics**.

3. Klicka på **avancerade inställningar**.

4. Klicka på **Data**, klicka sedan på **Windows eller Linux-prestandaräknare**. Det finns en lista med standard-räknarna som du kan välja att aktivera och du kan ställa in intervallet för samlingen också. Du kan också lägga till [ytterligare prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) att samla in. Felaktigt format refereras till i den här [artikeln](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Klicka på **spara**, klicka sedan på **OK**.

6. Stäng bladet avancerade inställningar.

7. Under rubriken Allmänt, klickar du på **översikt**.

8. Paneler i form av ett diagram visas för var och en av de lösningar som är aktiverad, inklusive ett för Service Fabric. Klicka på den **Service Fabric** graph för att fortsätta till Service Fabric-analys-lösningen.

9. Du ser några paneler med diagram på användningskanal och reliable services-händelser. Grafisk representation av de data som flödar i för räknarna som du har valt visas under noden mått. 

10. Klicka på ett diagram för behållaren mått du vill ha ytterligare information. Du kan också fråga på prestandaräknardata på samma sätt som klusterhändelser och filtrerar på noder, perf räknarens namn och värden med hjälp av det Kusto-frågespråket.

![Log Analytics perf-räknare fråga](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Nästa steg

* Samla in relevanta [prestandaräknare](service-fabric-diagnostics-event-generation-perf.md). Om du vill konfigurera Log Analytics-agenten för att samla in specifika prestandaräknare, granska [konfigurera datakällor](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurera Log Analytics för att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik
* Alternativt kan du samla in prestandaräknare via [Azure Diagnostics-tillägg och skicka dem till Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
