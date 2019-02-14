---
title: Skapa aviseringar med Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs hur du kan skapa anpassade Azure-aviseringar baserat på loggfrågor för minne och CPU-belastningen från Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238860"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>Hur du konfigurerar aviseringar i Azure Monitor för behållaren prestandaproblem
Azure Monitor för behållare Övervakare prestanda för behållararbetsbelastningar distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). 

Den här artikeln beskriver hur du aktiverar aviseringar när användning av processor och minne på noder i klustret överskrider tröskeln för ditt definierade.

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>Skapa avisering på CPU-kluster eller minnesanvändning
För att Avisera när CPU eller minne användning är hög för ett kluster, kan du skapa en varningsregel för metriska måttenheter som baseras på loggfrågor tillhandahålls. Frågorna jämföra ett datetime-värde till finns med i `now` operatorn och går tillbaka en timme. Alla datum som lagras av Azure Monitor för behållare är i UTC-format.  

Innan du startar, om du inte är bekant med aviseringar i Azure Monitor kan du läsa [översikt över aviseringar i Microsoft Azure](../platform/alerts-overview.md). Läs mer om aviseringar via loggfrågor i [Loggaviseringar i Azure Monitor](../platform/alerts-unified-log.md)

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **övervakaren** från det vänstra fönstret i Azure-portalen. Under den **Insights** väljer **behållare**.    
3. Från den **övervakas kluster** väljer du ett kluster i listan genom att klicka på namnet på klustret.
4. I det vänstra fönstret under den **övervakning** väljer **loggar** att öppna i Azure Monitor loggar sidan, som används för att skriva och köra Azure Log Analytics-frågor.
5. På den **loggar** klickar du på **+ ny aviseringsregel**.
6. Under den **villkor** klickar du på den fördefinierade anpassade loggvillkor **när den anpassade loggsökning är <logic undefined>** . Den **anpassade loggsökning** signaltyp väljs automatiskt för oss eftersom startade vi skapar en varningsregel direkt från sidan för Azure Monitor-loggar.  
7. Klistra in någon av frågorna nedan i den **sökfråga** fält. Följande fråga beräknar genomsnittliga CPU-belastningen som ett genomsnitt av medlem noder CPU-belastning varje minut.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    Följande fråga beräknar den genomsnittliga minnesanvändningen som ett genomsnitt av minnesanvändningen för medlemmen noder varje minut.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. Konfigurera aviseringen med följande information:

    a. I listrutan **Baserat på** väljer du **Metrisk måttenhet**. Ett metriskt mått skapar en avisering för varje objekt i frågan med ett värde som överstiger det angivna tröskelvärdet.  
    b. För den **villkor**väljer **är större än** och ange **75** som en inledande baslinjekopia **tröskelvärdet** eller ange ett värde som uppfyller dina kriterier.  
    c. Under **utlösare avisering baserat på** väljer **efterföljande överträdelser** och väljer den nedrullningsbara listan **är större än** anger du värdet **2**.  
    d. Under **Evaluated utifrån** avsnittet, ändra den **Period** värdet till 60 minuter. Regeln ska köras var femte minut och returnerar poster som har skapats i den senaste timmen från den aktuella tiden. Om tidsperioden ställs in på ett bredare fönster tas risken för datafördröjning med i beräkningen, och det ser till att frågan returnerar data för att undvika ett falskt negativt svar där aviseringen aldrig utlöses. 

9. Slutför aviseringsregeln genom att klicka på **Spara**.
10. Ange ett namn på aviseringen i den **varningsregelns namn** fält. Ange en **beskrivning** över informationen för för aviseringen och välj en lämplig allvarlighetsgrad från alternativen.
11. Du kan aktivera aviseringsregeln omedelbart genom att acceptera standardvärdet för **Aktivera regel när du skapar**.
12. Det sista steget ska du välja en befintlig eller skapa en ny **åtgärdsgrupp**, vilket säkerställer att samma åtgärder vidtas för varje gång en avisering har utlösts och kan användas för varje regel som du definierar. Konfigurera baserat på hur IT-avdelningen eller DevOps operations hanterar incidenter. 
13. Färdigställ aviseringsregeln genom att klicka på **Skapa aviseringsregel**. Den börjar köras omedelbart.

## <a name="next-steps"></a>Nästa steg

* Gå igenom några av de [logga fråga exempel](container-insights-analyze.md#search-logs-to-analyze-data) Lär dig mer om fördefinierade frågor eller exempel utvärdera och använda eller anpassa för andra aviseringar scenarier. 
* Om du vill lära dig hur du använder Azure Monitor och övervaka andra aspekter av AKS-klustret går [visa Azure Kubernetes Service health](container-insights-analyze.md)
