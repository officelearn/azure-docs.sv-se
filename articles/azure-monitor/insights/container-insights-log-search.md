---
title: Så här frågar du efter loggar från Azure Monitor för behållare | Microsoft Docs
description: Azure Monitor för behållare samlar in Mät värden och loggdata och den här artikeln beskriver posterna och innehåller exempel frågor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 787e9e6d0ae86568e1af74b4d67fb716841a02df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477082"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Så här frågar du efter loggar från Azure Monitor för behållare

Azure Monitor för behållare samlar in prestanda statistik, inventerings data och hälso tillstånds information från behållar värdar och behållare, och vidarebefordrar den till arbets ytan Log Analytics i Azure Monitor. Data samlas in var tredje minut. Dessa data är tillgängliga för [fråga](../../azure-monitor/log-query/log-query-overview.md) i Azure Monitor. Du kan använda dessa data i scenarier som omfattar migrerings planering, kapacitets analys, identifiering och prestanda fel sökning på begäran.

## <a name="container-records"></a>Container poster

Exempel på poster som samlas in av Azure Monitor för behållare och de data typer som visas i loggs öknings resultatet visas i följande tabell:

| Datatyp | Datatyp i loggs ökning | Fält |
| --- | --- | --- |
| Prestanda för värdar och behållare | `Perf` | Dator, ObjectName, CounterName &#40;% processor tid, disk läsningar MB, disk skrivningar MB, minnes användning MB, nätverks mottagning byte, nätverks sändning byte, processor användning SEC,&#41;nätverk, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Container inventering | `ContainerInventory` | TimeGenerated, dator, container namn, ContainerHostname, image, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Behållar logg | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, behållar namn, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventering av container nod | `ContainerNodeInventory`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventering av poddar i ett Kubernetes-kluster | `KubePodInventory` | TimeGenerated, dator, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, container status,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, kluster namn, PodIp, SourceSystem |
| Inventering av noder som ingår i ett Kubernetes-kluster | `KubeNodeInventory` | TimeGenerated, dator, kluster namn, ClusterId, LastTransitionTimeReady, etiketter, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-händelser | `KubeEvents` | TimeGenerated, dator, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, meddelande, SourceSystem | 
| Tjänster i Kubernetes-klustret | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Prestanda mått för noder som ingår i Kubernetes-klustret | Perf &#124; där ObjectName = = "K8SNode" | Dator, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Prestanda mått för container delar i Kubernetes-klustret | Perf &#124; där ObjectName = = "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Anpassade mått |`InsightsMetrics` | Dator, namn, namn område, ursprung, SourceSystem, Taggar<sup>1</sup>, TimeGenerated, typ, va, _ResourceId | 

<sup>1</sup> egenskapen *taggar* representerar [flera dimensioner](../platform/data-platform-metrics.md#multi-dimensional-metrics) för motsvarande mått. Mer information om de mått som samlas in och lagras i `InsightsMetrics` tabellen och en beskrivning av post egenskaperna finns i [Översikt över InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>Stöd för Prometheus är en funktion i offentlig för hands version för tillfället.
>

## <a name="search-logs-to-analyze-data"></a>Sök i loggar för att analysera data

Azure Monitor loggar kan hjälpa dig att söka efter trender, diagnostisera Flask halsar, prognostisera eller korrelera data som kan hjälpa dig att avgöra om den aktuella kluster konfigurationen fungerar optimalt. Fördefinierade loggs ökningar tillhandahålls så att du direkt kan börja använda eller anpassa för att returnera den information som du vill.

Du kan utföra interaktiv analys av data i arbets ytan genom att välja alternativet **Visa Kubernetes händelse loggar** eller **Visa behållar loggar** i förhands gransknings fönstret i list rutan **Visa i Analytics** . Sidan **loggs ökning** visas till höger om Azure Portal sidan som du var på.

![Analysera data i Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Behållaren loggar utdata som vidarebefordras till din arbets yta är STDOUT och STDERR. Eftersom Azure Monitor övervakar Azure-hanterad Kubernetes (AKS) samlas Kube inte in idag på grund av den stora mängden genererade data. 

### <a name="example-log-search-queries"></a>Exempel på loggs öknings frågor

Det är ofta användbart att skapa frågor som börjar med ett exempel eller två och sedan ändra dem så att de passar dina behov. För att hjälpa till att bygga mer avancerade frågor kan du experimentera med följande exempel frågor:

| Fråga | Beskrivning | 
|-------|-------------|
| ContainerInventory<br> &#124;projekt dator, namn, avbildning, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;återge tabell | Lista all information om livs cykeln för en behållare| 
| KubeEvents_CL<br> &#124;där inte (IsEmpty (Namespace_s))<br> &#124;Sortera efter TimeGenerated DESC<br> &#124;återge tabell | Kubernetes-händelser|
| ContainerImageInventory<br> &#124;sammanfatta AggregatedValue = Count () efter avbildning, ImageTag, kör | Bild inventering | 
| **Välj visnings alternativ för linje diagram**:<br> Perf<br> &#124;där ObjectName = = "K8SContainer" och CounterName = = "cpuUsageNanoCores" &#124; sammanfatta AvgCPUUsageNanoCores = AVG (CounterValue) per bin (TimeGenerated, 30M), instancename | Container-CPU | 
| **Välj visnings alternativ för linje diagram**:<br> Perf<br> &#124;där ObjectName = = "K8SContainer" och CounterName = = "memoryRssBytes" &#124; sammanfatta AvgUsedRssMemoryBytes = AVG (CounterValue) per bin (TimeGenerated, 30M), instancename | Containerminne |
| InsightsMetrics<br> &#124;där name = = "requests_count"<br> &#124;sammanfatta val = any (val) av TimeGenerated = bin (TimeGenerated, 1m)<br> &#124;Sortera efter TimeGenerated ASC<br> &#124;projekt RequestsPerMinute = val-föreg (val), TimeGenerated <br> &#124;rendera Barchart  | Begär Anden per minut med anpassade mått |

## <a name="query-prometheus-metrics-data"></a>Fråga Prometheus Metrics-data

Följande exempel är en Prometheus mått fråga som visar disk läsningar per sekund per disk per nod.

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

Om du vill visa Prometheus-mått som har klippts av Azure Monitor filtrerade efter namnrymd anger du "Prometheus". Här är en exempel fråga som visar Prometheus-mått från namn området `default` Kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus-data kan också direkt frågas efter namn.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Fråga efter konfiguration eller OLE-fel

Följande exempel fråga returnerar informations händelser från `KubeMonAgentEvents` tabellen för att undersöka eventuella konfigurations-eller kassations fel.

```
KubeMonAgentEvents | where Level != "Info" 
```

Resultatet kommer att visa resultat som liknar följande:

![Logga frågeresultat av informations händelser från agenten](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Nästa steg

Azure Monitor för behållare innehåller inte en fördefinierad uppsättning aviseringar. Mer information om hur du skapar rekommenderade aviseringar för hög processor-och minnes användning finns i avsnittet [skapa prestanda aviseringar med Azure Monitor för behållare](container-insights-alerts.md) som stöder DevOps eller operativa processer och procedurer 
