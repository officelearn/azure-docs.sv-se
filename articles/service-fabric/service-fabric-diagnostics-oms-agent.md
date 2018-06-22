---
title: Azure Service Fabric - prestandaövervakning med Log Analytics | Microsoft Docs
description: Lär dig hur du ställer in Log Analytics agenten för övervakning av behållare och prestandaräknare för Azure Service Fabric-kluster.
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
ms.openlocfilehash: a31fe62f2e81a0e39e4c314fc736e91e72bf7517
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301561"
---
# <a name="performance-monitoring-with-log-analytics"></a>Prestandaövervakning med logganalys

Den här artikeln beskriver stegen för att lägga till Log Analytics-agenten som en virtuell dator skala ange tillägg i klustret och koppla den till befintlig Azure logganalys-arbetsytan. Detta gör att samla in diagnostikdata om behållare, program och prestandaövervakning. Genom att lägga till den som ett tillägg till virtuell dator scale set resurs, Azure Resource Manager ser till att det installeras på varje nod skalning även när klustret.

> [!NOTE]
> Den här artikeln förutsätter att du har en Azure logganalys-arbetsyta som redan har konfigurerat. Om du inte vill gå till [ställa in Azure logganalys](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Lägga till filnamnstillägget agenten via Azure CLI

Det bästa sättet att lägga till Log Analytics Agent till ditt kluster anges via virtuella datorns skaluppsättning tillgängliga API: er med Azure CLI. Om du inte har Azure CLI ännu, gå till Azure-portalen och öppna en [moln Shell](../cloud-shell/overview.md) instans, eller [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. När molnet gränssnittet begärs, kontrollera att du arbetar i samma prenumeration som din resurs. Kontrollera detta med `az account show` och kontrollera att värdet för ”name” matchar ditt kluster prenumeration.

2. Gå till resursgruppen där logganalys-arbetsytan finns i portalen. Klicka till logganalys-resurs (typ av resursen ska vara logganalys). När du är på översiktssidan för resursen klickar du på **avancerade inställningar** under avsnittet inställningar på den vänstra menyn.

    ![Log Analytics-egenskapssidan](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Klicka på **Windows-servrar** om du Ständiga in Windows-kluster och **Linux-servrar** om du skapar ett Linux-kluster. Den här sidan visas dina `workspace ID` och `workspace key` (visas som primärnyckel i portalen). Behöver du både för nästa steg.

4. Kör kommandot för att installera logganalys-agenten på ditt kluster med hjälp av den `vmss extension set` API i molnet gränssnittet:

    För ett Windows-kluster:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    För ett Linux-kluster:

    ```sh
    az vmss extension set --name Log AnalyticsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Här är ett exempel på Log Analytics agenten läggs till i ett Windows-kluster.

    ![Logga Analytics agent cli kommando](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Det bör ta mindre än 15 min ska kunna lägga till agenten noderna. Du kan kontrollera att agenterna har lagts till med hjälp av den `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Lägg till agenten via Resource Manager-mall

Exempel Resource Manager-mallar som distribuera ett Azure logganalys-arbetsytan och lägga till en agent till var och en av noderna är tillgänglig för [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) eller [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Du kan hämta och ändra den här mallen för att distribuera ett kluster som passar dig bäst.

## <a name="view-performance-counters"></a>Visa prestandaräknare

Nu när du har lagt till logganalys agenten head vill till logganalys-portalen för att välja vilka prestandaräknare samla in. 

1. Gå till resursgruppen som du skapade Service Fabric Analytics-lösning i Azure-portalen. Välj **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Klicka på **Log Analytics**.

3. Klicka på **avancerade inställningar**.

4. Klicka på **Data**, klicka på **Windows eller Linux-prestandaräknare**. Det finns en lista över standard räknare kan du aktivera och du kan ange intervall för samling för. Du kan också lägga till [ytterligare prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) att samla in. Rätt format är refererad i detta [artikel](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Klicka på **spara**, klicka på **OK**.

6. Stäng bladet avancerade inställningar.

7. Under rubriken Allmänt klickar du på **översikt**.

8. Paneler i form av ett diagram som visas för var och en av de lösningar som aktiverad, inklusive en för Service Fabric. Klicka på den **Service Fabric** diagram fortsätta till Service Fabric Analytics-lösningen.

9. Du ser några paneler med diagram på operativa kanalen och tillförlitlig services händelser. Grafisk representation av de data som flödar i för räknarna som du har valt visas under noden mått. 

10. Klicka på ett diagram för behållaren mått att visa ytterligare information. Du kan också fråga på prestandaräknardata på samma sätt som klusterhändelser och filtrera på noder, perf räknarens namn och värden med hjälp av frågespråket Kusto.

![Log Analytics perf räknaren fråga](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Nästa steg

* Samla in relevanta [prestandaräknare](service-fabric-diagnostics-event-generation-perf.md). Om du vill konfigurera agenten Log Analytics för att samla in specifika prestandaräknare, granska [konfigurera datakällor](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurera logganalys att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik
* Alternativt kan du samla in prestandaräknare via [tillägg för Azure-diagnostik och skicka dem till Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
