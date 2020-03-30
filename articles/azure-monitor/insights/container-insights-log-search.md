---
title: Fråga loggar från Azure Monitor för behållare | Microsoft-dokument
description: Azure Monitor for containers samlar in mått och loggdata och den här artikeln beskriver posterna och innehåller exempelfrågor.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333483"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Fråga loggar från Azure Monitor för behållare

Azure Monitor for containers samlar in prestandamått, lagerdata och hälsotillståndsinformation från behållare värdar och behållare och vidarebefordrar den till Log Analytics-arbetsytan i Azure Monitor. Data samlas in var tredje minut. Dessa data är tillgängliga för [frågor](../../azure-monitor/log-query/log-query-overview.md) i Azure Monitor. Du kan använda dessa data på scenarier som omfattar migreringsplanering, kapacitetsanalys, identifiering och felsökning av prestanda på begäran.

## <a name="container-records"></a>Behållarposter

Exempel på poster som samlas in av Azure Monitor för behållare och de datatyper som visas i loggsökresultat visas i följande tabell:

| Datatyp | Datatyp i loggsökning | Fält |
| --- | --- | --- |
| Prestanda för värdar och behållare | `Perf` | Dator, ObjectName, CounterName &#40;%Processortid, Diskläsning mb, diskskrivningar MB, MINNESanvändning MB, Nätverksmottagningsbyte, Nätverksskickbyte, Processoranvändning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning sek, Nätverksanvändning&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Lager för behållare | `ContainerInventory` | TimeGenerated, Dator, behållarnamn, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Behållare loggar | `ContainerLog` | TimeGenerated, Dator, bild-ID, behållarnamn, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Lager för containernod | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventering av poddar i ett Kubernetes-kluster | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Namn, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventering av noder som ingår i ett Kubernetes-kluster | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Etiketter, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-evenemang | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message, SourceSystem | 
| Tjänster i Kubernetes-klustret | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Prestandamått för noder som ingår i Kubernetes-klustret | Perf &#124; där ObjectName == "K8SNode" | Dator, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Prestandamått för behållardelen av Kubernetes-klustret | Perf &#124; där ObjectName == "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Anpassade mått |`InsightsMetrics` | Dator, Namn, Namnområde, Ursprung, SourceSystem, Taggar<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> *Egenskapen Taggar* representerar [flera dimensioner](../platform/data-platform-metrics.md#multi-dimensional-metrics) för motsvarande mått. Mer information om de mått som samlas `InsightsMetrics` in och lagras i tabellen och en beskrivning av postegenskaperna finns i [Översikt över InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Sök loggar för att analysera data

Azure Monitor Logs kan hjälpa dig att leta efter trender, diagnostisera flaskhalsar, prognos eller korrelera data som kan hjälpa dig att avgöra om den aktuella klusterkonfigurationen fungerar optimalt. Fördefinierade loggsökningar tillhandahålls för att du omedelbart ska börja använda eller anpassa för att returnera informationen som du vill.

Du kan utföra interaktiv analys av data på arbetsytan genom att välja **händelseloggar för Visa Kubernetes** eller **Visa behållarloggar** i förhandsgranskningsfönstret i listrutan **Visa i analyser.** Sidan **Loggsökning** visas till höger om den Azure-portalsida som du var på.

![Analysera data i Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Utdata för behållarloggar som vidarebefordras till arbetsytan är STDOUT och STDERR. Eftersom Azure Monitor övervakar Azure-hanterade Kubernetes (AKS) samlas Kube-systemet inte in idag på grund av den stora mängden genererade data. 

### <a name="example-log-search-queries"></a>Exempel på loggsökningsfrågor

Det är ofta användbart att skapa frågor som börjar med ett exempel eller två och sedan ändra dem så att de passar dina behov. Om du vill skapa mer avancerade frågor kan du experimentera med följande exempelfrågor:

| Söka i data | Beskrivning | 
|-------|-------------|
| ContainerInventory<br> &#124; projektdator, namn, image, imagetag, containerstate, createdtime, startedtime, finishedtime<br> &#124; återge tabell | Lista all livscykelinformation för en behållare| 
| KubeEvents_CL<br> &#124; där inte (isempty(Namespace_s))<br> &#124; sortera efter TimeGenerated desc<br> &#124; återge tabell | Kubernetes händelser|
| ContainerImageInventory<br> &#124; sammanfatta aggregeradvärde = antal() efter bild, ImageTag, Löpning | Bildinventering | 
| **Välj visningsalternativet linjediagram:**<br> Prest<br> &#124; där ObjectName == "K8SContainer" och CounterName == "cpuUsageNanoCores" &#124; sammanfatta AvgCPUUsageNanoCores = avg(CounterValue) efter bin(TimeGenerated, 30m), InstanceName | Behållare CPU | 
| **Välj visningsalternativet linjediagram:**<br> Prest<br> &#124; där ObjectName == "K8SContainer" och CounterName == "memoryRssBytes" &#124; sammanfatta AvgUsedRssMemoryBytes = avg(CounterValue) efter lagerplats(TimeGenerated, 30m), InstanceName | Behållarminne |
| InsikterMetri<br> &#124; där Namn == "requests_count"<br> &#124; sammanfatta Val=any(Val) efter TimeGenerated=bin(TimeGenerated, 1m)<br> &#124; sortera efter TimeGenerated asc<br> &#124; projekt RequestsPerMinute = Val - föregående(Val), TimeGenerated <br> &#124; återge stapeldiagram  | Begäranden per minut med anpassade mått |

## <a name="query-prometheus-metrics-data"></a>Frågetaletheus-mätdata

Följande exempel är en Prometheus-måttfråga som visar diskläsningar per sekund per disk per nod.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Om du vill visa Prometheus-mått som skrapats av Azure Monitor filtrerat efter namnområde anger du "prometheus". Här är en exempelfråga för att visa `default` Prometheus-mått från namnområdet kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus data kan också direkt efterfrågas med namn.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Frågekonfiguration eller skrapningsfel

Om du vill undersöka eventuella konfigurations- eller skrapningsfel returnerar följande exempel informationshändelser från tabellen. `KubeMonAgentEvents`

```
KubeMonAgentEvents | where Level != "Info" 
```

Utdata visar resultat som liknar följande:

![Logga frågeresultat för informationshändelser från agent](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Nästa steg

Azure Monitor för behållare innehåller inte en fördefinierad uppsättning aviseringar. Granska [skapa prestandaaviseringar med Azure Monitor för behållare för](container-insights-alerts.md) att lära dig hur du skapar rekommenderade aviseringar för hög CPU- och minnesanvändning för att stödja dina DevOps eller operativa processer och procedurer. 
