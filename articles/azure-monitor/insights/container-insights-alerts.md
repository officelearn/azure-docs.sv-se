---
title: Skapa aviseringar med hjälp av Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Monitor för behållare för att skapa anpassade varningar utifrån loggfrågor för minne och CPU-användning.
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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: bbd7c733c7c089328d2fbe016426fe9de3a6b5ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494634"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Hur du ställer in aviseringar för problem med prestanda i Azure Monitor för behållare
Azure Monitor för behållare övervakar prestanda för arbetsbelastningar som distribueras till Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS).

Den här artikeln beskriver hur du aktiverar aviseringar i följande situationer:

* När CPU eller minne användning på klusternoder överskrider ett angivet tröskelvärde
* När CPU eller minne användning på alla behållare i en kontrollant överskrider ett angivet tröskelvärde jämfört med en gräns som har angetts i motsvarande resurs
* *NotReady* komponentstatusnoden räknar
*  *Det gick inte*, *väntande*, *okänd*, *kör*, eller *lyckades* pod-fas räknar

Använd de frågor som tillhandahålls för att skapa en metrisk varning eller en avisering om metriska måttenheter varnas för hög CPU- eller minnesanvändning på klusternoder. Måttaviseringar har kortare svarstider än loggaviseringar. Men aviseringar ger avancerade frågor och större grad. Loggaviseringar frågor jämföra ett datetime aktuella med hjälp av den *nu* operatorn och gå tillbaka en timme. (Azure Monitor för behållare lagrar alla datum i Coordinated Universal Time (UTC)-format.)

Om du inte är bekant med Azure Monitor-aviseringar finns i [översikt över aviseringar i Microsoft Azure](../platform/alerts-overview.md) innan du börjar. Läs mer om aviseringar som använder loggfrågor i [Loggaviseringar i Azure Monitor](../platform/alerts-unified-log.md). Mer information om måttaviseringar finns i [måttaviseringar i Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Loggsökningsfrågor för resurs-användning
Frågorna i det här avsnittet stöder varje aviseringar scenario. De används i steg 7 i den [skapa avisering](#create-an-alert-rule) i den här artikeln.

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

Följande fråga beräknar genomsnittliga minnesanvändningen som ett genomsnitt av medlem noder minnesanvändningen varje minut.

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
>Följande frågor använder platshållarvärdena \<your klusternamn > och \<your domänkontrollantens namn > som representerar ditt kluster och en domänkontrollant. Ersätt dem med värden som är specifika för din miljö när du konfigurerar aviseringar.

Följande fråga beräknar genomsnittliga CPU-utnyttjande på alla behållare i en kontroll som ett genomsnitt av CPU-belastning för varje container-instans som en kontrollant varje minut. Måttet är en del av gränsen som ställts in för en behållare.

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

Följande fråga beräknar den genomsnittliga minnesanvändningen för alla behållare i en kontroll som ett genomsnitt av minnesanvändningen för varje container-instans som en kontrollant varje minut. Måttet är en del av gränsen som ställts in för en behållare.

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

Följande fråga returnerar alla noder och antalet som har statusen *redo* och *NotReady*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
I följande fråga returnerar pod fas räknar baserat på alla faser: *Det gick inte*, *väntande*, *okänd*, *kör*, eller *lyckades*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Att Avisera om vissa pod-faser, till exempel *väntande*, *misslyckades*, eller *okänd*, ändra den sista raden i frågan. Till exempel till en avisering för *FailedCount* använder: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>Skapa en varningsregel
Följ dessa steg för att skapa en avisering om loggen i Azure Monitor med någon av de log search regler som angavs tidigare.  

>[!NOTE]
>Följande procedur för att skapa en aviseringsregel för behållaren resursutnyttjande kräver att du kan växla till en ny loggfil aviseringar API som beskrivs i [växla API till inställningar för loggaviseringar](../platform/alerts-log-api-switch.md).
>

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **övervakaren** från fönstret till vänster. Under **Insights**väljer **behållare**.
3. På den **övervakas kluster** väljer du ett kluster i listan.
4. I fönstret till vänster under **övervakning**väljer **loggar** att öppna sidan för Azure Monitor-loggar. Du kan använda den här sidan för att skriva och köra Azure Log Analytics-frågor.
5. På den **loggar** väljer **+ ny aviseringsregel**.
6. I den **villkor** väljer den **när den anpassade loggsökning är \<logic Odefinierad >** fördefinierade anpassade loggvillkor. Den **anpassade loggsökning** signaltyp väljs automatiskt eftersom vi skapar en varningsregel direkt från sidan för Azure Monitor-loggar.  
7. Klistra in ett av de [frågor](#resource-utilization-log-search-queries) visas tidigare i den **sökfråga** fält.
8. Konfigurera aviseringen enligt följande:

    1. I listrutan **Baserat på** väljer du **Metrisk måttenhet**. En metrisk måttenhet skapar en avisering för varje objekt i den fråga som innehåller ett värde över våra angivet tröskelvärde.
    1. För **villkor**väljer **är större än**, och ange **75** som en inledande baslinjekopia **tröskelvärdet**. Eller ange ett annat värde som uppfyller dina kriterier.
    1. I den **utlösare avisering baserat på** väljer **efterföljande överträdelser**. Från listrutan, väljer **är större än**, och ange **2**.
    1. Konfigurera en avisering för behållare CPU eller minne utnyttjande under **sammanställda på**väljer **ContainerName**. 
    1. I den **Evaluated utifrån** anger den **Period** värde att **60 minuter**. Regeln körs var femte minut och returnerar poster som har skapats i den senaste timmen från den aktuella tiden. Ställa in hur lång tid en bred fönstret konton för potentiella datafördröjning. Det innebär också att frågan returnerar data för att undvika falska negativt där aviseringen aldrig utlöses.

9. Välj **klar** att slutföra varningsregeln.
10. Ange ett namn i den **varningsregelns namn** fält. Ange en **beskrivning** som ger information om aviseringen. Välja en lämplig allvarlighetsgrad från alternativen.
11. Om du vill aktivera varningsregeln omedelbart, godkänner du standardvärdet för **aktivera regeln vid skapande**.
12. Välj en befintlig **åtgärdsgrupp** eller skapa en ny grupp. Det här steget säkerställer att samma åtgärder vidtas för varje gång som en avisering utlöses. Konfigurera baserat på hur IT-avdelningen eller DevOps driftsteamet hanterar incidenter.
13. Välj **skapa varningsregel** att slutföra varningsregeln. Den börjar köras omedelbart.

## <a name="next-steps"></a>Nästa steg

- Visa [logga fråga exempel](container-insights-log-search.md#search-logs-to-analyze-data) att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisera och analysera dina kluster.
- Läs mer om Azure Monitor och övervaka andra aspekter av AKS-klustret i [visa Azure Kubernetes Service health](container-insights-analyze.md).
