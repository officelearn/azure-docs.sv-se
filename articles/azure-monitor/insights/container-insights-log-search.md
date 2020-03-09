---
title: Så här frågar du efter loggar från Azure Monitor för behållare | Microsoft Docs
description: Azure Monitor för behållare samlar in Mät värden och loggdata och den här artikeln beskriver posterna och innehåller exempel frågor.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: dcd1656673e549b583de26bca897d0055f389d0a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362251"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Så här frågar du efter loggar från Azure Monitor för behållare

Azure Monitor för behållare samlar in prestanda statistik, inventerings data och hälso tillstånds information från behållar värdar och behållare, och vidarebefordrar den till arbets ytan Log Analytics i Azure Monitor. Data som samlas in var tredje minut. Dessa data är tillgängliga för [fråga](../../azure-monitor/log-query/log-query-overview.md) i Azure Monitor. Du kan använda dessa data i scenarier som omfattar migrerings planering, kapacitets analys, identifiering och prestanda fel sökning på begäran.

## <a name="container-records"></a>Behållarposter

Exempel på poster som samlas in av Azure Monitor för behållare och vilka datatyper av som visas i sökresultaten för loggen visas i följande tabell:

| Datatyp | Datatypen i Loggsökning | Fält |
| --- | --- | --- |
| Prestanda för värdar och behållare | `Perf` | Datorn, objektnamn, CounterName &#40;läser MB tid i procent för Processor, Disk, Disk skriver MB, MB för användning av minne, nätverk ta emot byte, nätverk skicka byte, Processor användning sek, Network&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem |
| Behållare-inventering | `ContainerInventory` | TimeGenerated, dator, behållarnamn ContainerHostname, bild, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommandot, CreatedTime, StartedTime, FinishedTime, SourceSystem, behållar-ID, ImageID |
| Behållarloggen | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, namn, LogEntrySource, LogEntry, SourceSystem, behållar-ID |
| Behållarnodslager | `ContainerNodeInventory`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventering av poddar i ett Kubernetes-kluster | `KubePodInventory` | TimeGenerated, dator, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, container status,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, kluster namn, PodIp, SourceSystem |
| Inventering av noder tillhör ett Kubernetes-kluster | `KubeNodeInventory` | TimeGenerated, dator, klusternamn, ClusterId, LastTransitionTimeReady, etiketter, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-händelser | `KubeEvents` | TimeGenerated, dator, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, meddelande, SourceSystem | 
| Tjänster i Kubernetes-kluster | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Prestandamått för noder som en del av Kubernetes-kluster | Perf &#124; där ObjectName == ”K8SNode” | Dator, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Prestandamått för behållare som en del av Kubernetes-kluster | Perf &#124; där ObjectName == ”K8SContainer” | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Anpassade mått |`InsightsMetrics` | Dator, namn, namn område, ursprung, SourceSystem, Taggar<sup>1</sup>, TimeGenerated, typ, Va, _ResourceId | 

<sup>1</sup> egenskapen *taggar* representerar [flera dimensioner](../platform/data-platform-metrics.md#multi-dimensional-metrics) för motsvarande mått. Mer information om de mått som samlas in och lagras i `InsightsMetrics` tabellen och en beskrivning av post egenskaperna finns i [Översikt över InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>Stöd för Prometheus är en funktion i offentlig för hands version för tillfället.
>

## <a name="search-logs-to-analyze-data"></a>Sök i loggar att analysera data

Azure Monitor loggar kan hjälpa dig att söka efter trender, diagnostisera Flask halsar, prognostisera eller korrelera data som kan hjälpa dig att avgöra om den aktuella kluster konfigurationen fungerar optimalt. Fördefinierade loggsökningar tillhandahålls för du omedelbart börja använda eller anpassa för att returnera informationen som du vill.

Du kan utföra interaktiv analys av data i arbets ytan genom att välja alternativet **Visa Kubernetes händelse loggar** eller **Visa behållar loggar** i förhands gransknings fönstret i list rutan **Visa i Analytics** . Sidan **loggs ökning** visas till höger om Azure Portal sidan som du var på.

![Analysera data i Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Behållaren loggar utdata som vidarebefordras till din arbets yta är STDOUT och STDERR. Eftersom Azure Monitor övervakar Azure-hanterade Kubernetes (AKS), Kube system som inte samlas in idag på grund av den stora mängden skapas. 

### <a name="example-log-search-queries"></a>Exempel loggsökningsfrågor

Det är ofta bra att skapa frågor som börjar med ett exempel eller två och ändra dem efter dina behov. För att skapa mer avancerade frågor kan experimentera du med följande exempelfrågor:

| Fråga | Beskrivning | 
|-------|-------------|
| ContainerInventory<br> &#124;projektet dator, namn, bild, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;Rendera tabell | Visa en lista över information om en behållarens livslängd| 
| KubeEvents_CL<br> &#124;där not(isempty(Namespace_s))<br> &#124;Sortera efter TimeGenerated fall<br> &#124;Rendera tabell | Kubernetes-händelser|
| ContainerImageInventory<br> &#124;Sammanfatta AggregatedValue = antal() efter bild, ImageTag, körs | Avbildningslager | 
| **Välj visnings alternativ för linje diagram**:<br> Perf<br> &#124;där ObjectName == ”K8SContainer” och CounterName == ”cpuUsageNanoCores” &#124; sammanfatta AvgCPUUsageNanoCores = avg(CounterValue) efter bin (TimeGenerated, 30m), instansnamn | Processorprestanda för behållare | 
| **Välj visnings alternativ för linje diagram**:<br> Perf<br> &#124;där ObjectName == ”K8SContainer” och CounterName == ”memoryRssBytes” &#124; sammanfatta AvgUsedRssMemoryBytes = avg(CounterValue) efter bin (TimeGenerated, 30m), instansnamn | Behållare-minne |
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
