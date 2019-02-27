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
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: e6fdb0d57a44578647c1f16dc76c557296f20ddb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886791"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Hur du ställer in aviseringar för problem med prestanda i Azure Monitor för behållare
Azure Monitor för behållare Övervakare prestanda för behållararbetsbelastningar distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). 

Den här artikeln beskriver hur du aktiverar aviseringar i följande situationer:

* När användning av processor och minne på noder i klustret eller överskrider tröskeln för ditt definierade.
* När CPU eller minne användning på någon av behållarna i en kontrollant överskrider tröskeln för ditt definierade jämfört med den angivna gränsen på motsvarande resurs.

För att Avisera när CPU eller minne användning är hög för ett kluster eller en domänkontrollant, kan du skapa en varningsregel för metriska måttenheter som baseras på loggfrågor tillhandahålls. Frågorna jämför ett datetime det aktuella med hjälp av operatorn nu och går tillbaka en timme. Alla datum som lagras av Azure Monitor för behållare är i UTC-format.

Innan du startar, om du inte är bekant med aviseringar i Azure Monitor kan du läsa [översikt över aviseringar i Microsoft Azure](../platform/alerts-overview.md). Läs mer om aviseringar via loggfrågor i [Loggaviseringar i Azure Monitor](../platform/alerts-unified-log.md)

## <a name="resource-utilization-log-search-queries"></a>Loggsökningsfrågor för resurs-användning
Frågorna i det här avsnittet som stöd för varje aviseringar scenario. Frågorna som krävs för steg 7 under den [skapa avisering](#create-alert-rule) nedan.  

Följande fråga beräknar genomsnittliga CPU-belastningen som ett genomsnitt av medlem noder CPU-belastning varje minut.  

```kusto
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

```kusto
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
>[!IMPORTANT]
>Frågorna nedan innehåller platshållare strängvärden för ditt kluster och controller namn - < your-kluster-name > och < your-controller-name >. Ersätt platshållarna med värden som är specifika för din miljö innan du konfigurerar aviseringar. 


Följande fråga beräknar genomsnittliga CPU-utnyttjande på alla behållare i en kontroll som ett genomsnitt av CPU-belastning för varje container-instans som en domänkontrollant som en procentandel av den gränsen som ställts in för en behållare i minuten.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Följande fråga beräknar den genomsnittliga minnesanvändningen för alla behållare i en kontroll som ett genomsnitt av minnesanvändningen för varje container-instans som en domänkontrollant som en procentandel av den gränsen som ställts in för en behållare i minuten.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="create-alert-rule"></a>Skapa aviseringsregel
Utför följande steg för att skapa en Log-aviseringar i Azure Monitor med någon av de log search regler som angavs tidigare.  

>[!NOTE]
>Proceduren nedan måste du växla till nya aviseringar för Log-API som beskrivs i [växla API till inställningar för Loggaviseringar](../platform/alerts-log-api-switch.md) om du skapar en varningsregel för behållaren resursutnyttjande. 
>

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **övervakaren** från det vänstra fönstret i Azure-portalen. Under den **Insights** väljer **behållare**.    
3. Från den **övervakas kluster** väljer du ett kluster i listan genom att klicka på namnet på klustret.
4. I det vänstra fönstret under den **övervakning** väljer **loggar** att öppna i Azure Monitor loggar sidan, som används för att skriva och köra Azure Log Analytics-frågor.
5. På den **loggar** klickar du på **+ ny aviseringsregel**.
6. Under den **villkor** klickar du på den fördefinierade anpassade loggvillkor **när den anpassade loggsökning är <logic undefined>** . Den **anpassade loggsökning** signaltyp väljs automatiskt för oss eftersom startade vi skapar en varningsregel direkt från sidan för Azure Monitor-loggar.  
7. Klistra in ett av de [frågor](#resource-utilization-log-search-queries) visas tidigare i den **sökfråga** fält. 

8. Konfigurera aviseringen med följande information:

    a. I listrutan **Baserat på** väljer du **Metrisk måttenhet**. Ett metriskt mått skapar en avisering för varje objekt i frågan med ett värde som överstiger det angivna tröskelvärdet.  
    b. För den **villkor**väljer **är större än** och ange **75** som en inledande baslinjekopia **tröskelvärdet** eller ange ett värde som uppfyller dina kriterier.  
    c. Under **utlösare avisering baserat på** väljer **efterföljande överträdelser** och väljer den nedrullningsbara listan **är större än** anger du värdet **2**.  
    d. Om du konfigurerar en avisering för behållare CPU eller minne utnyttjande under **sammanställda på** Välj **ContainerName** från den nedrullningsbara listan.  
    e. Under **Evaluated utifrån** avsnittet, ändra den **Period** värdet till 60 minuter. Regeln ska köras var femte minut och returnerar poster som har skapats i den senaste timmen från den aktuella tiden. Om tidsperioden ställs in på ett bredare fönster tas risken för datafördröjning med i beräkningen, och det ser till att frågan returnerar data för att undvika ett falskt negativt svar där aviseringen aldrig utlöses. 

9. Slutför aviseringsregeln genom att klicka på **Spara**.
10. Ange ett namn på aviseringen i den **varningsregelns namn** fält. Ange en **beskrivning** över informationen för för aviseringen och välj en lämplig allvarlighetsgrad från alternativen.
11. Du kan aktivera aviseringsregeln omedelbart genom att acceptera standardvärdet för **Aktivera regel när du skapar**.
12. Det sista steget ska du välja en befintlig eller skapa en ny **åtgärdsgrupp**, vilket säkerställer att samma åtgärder vidtas för varje gång en avisering har utlösts och kan användas för varje regel som du definierar. Konfigurera baserat på hur IT-avdelningen eller DevOps operations hanterar incidenter. 
13. Färdigställ aviseringsregeln genom att klicka på **Skapa aviseringsregel**. Den börjar köras omedelbart.

## <a name="next-steps"></a>Nästa steg

* Gå igenom några av de [logga fråga exempel](container-insights-analyze.md#search-logs-to-analyze-data) Lär dig mer om fördefinierade frågor eller exempel utvärdera och använda eller anpassa för andra aviseringar scenarier. 
* Om du vill lära dig hur du använder Azure Monitor och övervaka andra aspekter av AKS-klustret går [visa Azure Kubernetes Service health](container-insights-analyze.md)
