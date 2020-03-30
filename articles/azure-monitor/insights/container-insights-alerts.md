---
title: Skapa prestandaaviseringar för Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs hur du skapar anpassade aviseringar baserat på loggfrågor för minnes- och CPU-användning från Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730933"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Konfigurera aviseringar om prestandaproblem i Azure Monitor för containrar

Azure Monitor for containers övervakar prestanda för behållararbetsbelastningar som distribueras till Azure Container Instances eller till hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS).

I den här artikeln beskrivs hur du aktiverar aviseringar för följande situationer:

- När CPU- eller minnesanvändning på klusternoder överskrider ett tröskelvärde
- När CPU- eller minnesanvändning på en behållare i en styrenhet överskrider ett tröskelvärde jämfört med en gräns som anges på motsvarande resurs
- *Nodantal för notReady-status*
- *Det gick inte*, *Väntar*, *Okänd*, *Körs*eller *Lyckades* antal pod-fas
- När ledigt diskutrymme på klusternoder överskrider ett tröskelvärde 

Om du vill varna för hög CPU- eller minnesanvändning, eller låg ledigt diskutrymme på klusternoder, använder du de frågor som tillhandahålls för att skapa en måttavisering eller en måttmätningsavisering. Måttaviseringar har lägre svarstid än loggaviseringar. Men loggvarningar ger avancerad frågor och större förfining. Loggvarningar frågor jämföra en datetime till den nuvarande med hjälp av *nu* operatorn och gå tillbaka en timme. (Azure Monitor för behållare lagrar alla datum i UTC-format (Coordinated Universal Time).

Om du inte är bekant med Azure Monitor-aviseringar läser du [Översikt över aviseringar i Microsoft Azure](../platform/alerts-overview.md) innan du börjar. Mer information om aviseringar som använder loggfrågor finns [i Loggaviseringar i Azure Monitor](../platform/alerts-unified-log.md). Mer information om måttaviseringar finns [i Måttaviseringar i Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Sökfrågor för resursanvändningsloggar

Frågorna i det här avsnittet stöder varje aviseringsscenario. De används i steg 7 i avsnittet [skapa varning](#create-an-alert-rule) i den här artikeln.

Följande fråga beräknar genomsnittlig CPU-användning som ett genomsnitt av medlemsnodernas CPU-användning varje minut.  

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

Följande fråga beräknar genomsnittlig minnesanvändning som ett genomsnitt av medlemsnodernas minnesanvändning varje minut.

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
>Följande frågor använder platshållarvärdena \<som> och> \<som styrenhet och> för att representera klustret och handkontrollen. Ersätt dem med värden som är specifika för din miljö när du ställer in aviseringar.

Följande fråga beräknar den genomsnittliga CPU-användningen av alla behållare i en styrenhet som ett genomsnitt av CPU-användning av varje behållarinstans i en styrenhet varje minut. Mätningen är en procentandel av den gräns som ställts in för en behållare.

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

Följande fråga beräknar den genomsnittliga minnesanvändningen av alla behållare i en styrenhet som ett genomsnitt av minnesanvändningen för varje behållarinstans i en styrenhet varje minut. Mätningen är en procentandel av den gräns som ställts in för en behållare.

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

Följande fråga returnerar alla noder och antal som har *statusen Klar* och *NotReady*.

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
Följande fråga returnerar pod-fasantal baserat på alla faser: *Misslyckades*, *Väntande*, *Okänd*, *Kör*eller *Lyckades*.  

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
>Om du vill avisera på vissa pod-faser, till exempel *Väntande,* *Misslyckades*eller *Okänd,* ändrar du den sista raden i frågan. Om du till exempel vill avisera vid *FailedCount-användning:* <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Följande fråga returnerar klusternoder diskar som överstiger 90% ledigt utrymme som används. Om du vill hämta kluster-ID:et kör `ClusterId` du först följande fråga och kopierar värdet från egenskapen:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Skapa en varningsregel

Följ dessa steg för att skapa en loggavisering i Azure Monitor med hjälp av en av de loggsökregler som angavs tidigare. Information om hur du skapar med hjälp av en ARM-mall finns i [Skapa exempelloggavisering med Azure Resource Template](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template).

>[!NOTE]
>Följande procedur för att skapa en varningsregel för användning av behållarresurser kräver att du växlar till ett nytt loggaviserings-API enligt beskrivningen i [Switch API-inställningar för loggaviseringar](../platform/alerts-log-api-switch.md).
>

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter och välj **Log Analytics-arbetsytor**i Azure-portalen .
3. I listan över Log Analytics-arbetsytor väljer du arbetsytan som stöder Azure Monitor för behållare. 
4. I fönstret till vänster väljer du **Loggar** för att öppna sidan Azure Monitor-loggar. Du använder den här sidan för att skriva och köra Azure Log Analytics-frågor.
5. På sidan **Loggar** klistrar du in en av de frågor som [angavs](#resource-utilization-log-search-queries) tidigare i **sökfrågefältet** och väljer sedan **Kör** för att validera resultaten. Om du inte utför det här steget är alternativet **+Nytt avisering** inte tillgängligt att välja.
6. Välj **+Ny avisering** om du vill skapa en loggavisering.
7. I avsnittet **Villkor** väljer du **när den \<anpassade loggsökningen är logikdefinierad>fördefinierade** anpassade loggvillkor. Den **anpassade loggsöksignaltypen** väljs automatiskt eftersom vi skapar en varningsregel direkt från sidan Azure Monitor-loggar.  
8. Klistra in en av de frågor som [angavs](#resource-utilization-log-search-queries) tidigare i **sökfrågefältet.**
9. Konfigurera aviseringen enligt följande:

    1. Välj **Måttmätning**i listrutan **Baserat på** . En måttmätning skapar en avisering för varje objekt i frågan som har ett värde över vårt angivna tröskelvärde.
    1. För **Villkor**väljer du **Större än**och anger **75** som ett första baslinjetröskelvärde för cpu- och minnesanvändningsaviseringar. **Threshold** För den låga diskutrymmesvarningen anger du **90**. Eller ange ett annat värde som uppfyller dina kriterier.
    1. I avsnittet **Utlösarvarning baserat på** väljer du **På varandra följande överträdelser**. Välj **Större än**i listrutan och skriv **in 2**.
    1. Om du vill konfigurera en avisering för behållarens CPU- eller minnesanvändning väljer du **ContainerName**under **Mängd på**. Om du vill konfigurera för klusternod låg diskvarning väljer du **ClusterId**.
    1. I avsnittet **Utvärderat baserat på** anger du **periodvärdet** till **60 minuter**. Regeln körs var femte minut och returnerar poster som har skapats inom den senaste timmen från den aktuella tiden. Ställa in tidsperioden till ett brett fönster konton för potentiella data svarstid. Det säkerställer också att frågan returnerar data för att undvika ett falskt negativt där aviseringen aldrig utlöses.

10. Välj **Klar** för att slutföra varningsregeln.
11. Ange ett namn i fältet **Aviseringsregelnamn.** Ange en **beskrivning** som innehåller information om aviseringen. Och välj en lämplig allvarlighetsgrad från de alternativ som anges.
12. Om du omedelbart vill aktivera varningsregeln godkänner du standardvärdet för **Aktivera regel när du skapar**.
13. Välj en befintlig **åtgärdsgrupp** eller skapa en ny grupp. Det här steget säkerställer att samma åtgärder vidtas varje gång en avisering utlöses. Konfigurera baserat på hur din IT- eller DevOps-driftteam hanterar incidenter.
14. Välj **Skapa aviseringsregel** för att slutföra varningsregeln. Den börjar köras omedelbart.

## <a name="next-steps"></a>Nästa steg

- Visa [exempel på loggfrågor](container-insights-log-search.md#search-logs-to-analyze-data) om du vill se fördefinierade frågor och exempel som kan utvärderas eller anpassas för aviseringar, visualisering eller analys av kluster.
- Mer information om Azure Monitor och hur du övervakar andra aspekter av Kubernetes-klustret finns i [Visa Kubernetes klusterprestanda](container-insights-analyze.md) och [Visa kubernetes klusterhälsa](container-insights-health.md).
