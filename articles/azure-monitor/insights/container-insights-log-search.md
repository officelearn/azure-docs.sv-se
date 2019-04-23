---
title: Hur du fråga loggar från Azure Monitor för behållare | Microsoft Docs
description: Azure Monitor för behållare samlar in data för mått och loggfiler och den här artikeln beskriver posterna och innehåller exempelfrågor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014410"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Hur du frågar loggar från Azure Monitor för behållare
Azure Monitor för behållare samlar in prestandamått och lagerdata hälsotillståndsinformation från behållare-värdar och behållare och vidarebefordrar den till arbetsytan Log Analytics i Azure Monitor. Data som samlas in var tredje minut. Informationen är tillgänglig för [fråga](../../azure-monitor/log-query/log-query-overview.md) i Azure Monitor. Du kan använda dessa data för scenarier som omfattar planering av migreringsaktiviteter, kapacitetsanalys, identifiering och prestandafelsökning för på begäran.

## <a name="container-records"></a>Behållarposter

Exempel på poster som samlas in av Azure Monitor för behållare och vilka datatyper av som visas i sökresultaten för loggen visas i följande tabell:

| Datatyp | Datatypen i Loggsökning | Fält |
| --- | --- | --- |
| Prestanda för värdar och behållare | `Perf` | Datorn, objektnamn, CounterName &#40;läser MB tid i procent för Processor, Disk, Disk skriver MB, MB för användning av minne, nätverk ta emot byte, nätverk skicka byte, Processor användning sek, Network&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem |
| Behållare-inventering | `ContainerInventory` | TimeGenerated, dator, behållarnamn ContainerHostname, bild, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommandot, CreatedTime, StartedTime, FinishedTime, SourceSystem, behållar-ID, ImageID |
| Behållarloggen | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, namn, LogEntrySource, LogEntry, SourceSystem, behållar-ID |
| Behållarnodslager | `ContainerNodeInventory`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventering av poddar i ett Kubernetes-kluster | `KubePodInventory` | TimeGenerated, dator, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, behållar-ID, ContainerName, namn, PodLabel, Namespace, PodStatus, klusternamn, PodIp, SourceSystem |
| Inventering av noder tillhör ett Kubernetes-kluster | `KubeNodeInventory` | TimeGenerated, dator, klusternamn, ClusterId, LastTransitionTimeReady, etiketter, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-händelser | `KubeEvents` | TimeGenerated, dator, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, meddelande, SourceSystem | 
| Tjänster i Kubernetes-kluster | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Prestandamått för noder som en del av Kubernetes-kluster | Perf &#124; där ObjectName == ”K8SNode” | Datorn, objektnamn, CounterName &#40;cpuAllocatableBytes memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem | 
| Prestandamått för behållare som en del av Kubernetes-kluster | Perf &#124; där ObjectName == ”K8SContainer” | CounterName &#40; cpuRequestNanoCores memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem | 
| InsightsMetrics | Computer, Name, Namespace, Origin, SourceSystem, Tags, TimeGenerated, Type, Value |

## <a name="search-logs-to-analyze-data"></a>Sök i loggar att analysera data
Azure Monitor-loggar kan hjälpa dig att söka trender, diagnostisera flaskhalsar, prognoser och korrelera data som kan hjälpa dig att avgöra om den aktuella klusterkonfigurationen presterar optimalt. Fördefinierade loggsökningar tillhandahålls för du omedelbart börja använda eller anpassa för att returnera informationen som du vill. 

Du kan utföra interaktiva analyser av data på arbetsytan genom att välja den **visa Kubernetes-händelseloggar** eller **visa behållarloggarna** alternativet i förhandsgranskningsfönstret. Den **Loggsökning** visas till höger om Azure portal sidan som du var på.

![Analysera data i Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Utdata för container-loggar som vidarebefordras till din arbetsyta är STDOUT och STDERR. Eftersom Azure Monitor övervakar Azure-hanterade Kubernetes (AKS), Kube system som inte samlas in idag på grund av den stora mängden skapas. 

### <a name="example-log-search-queries"></a>Exempel loggsökningsfrågor
Det är ofta bra att skapa frågor som börjar med ett exempel eller två och ändra dem efter dina behov. För att skapa mer avancerade frågor kan experimentera du med följande exempelfrågor:

| Söka i data | Beskrivning | 
|-------|-------------|
| ContainerInventory<br> &#124;projektet dator, namn, bild, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;Rendera tabell | Visa en lista över information om en behållarens livslängd| 
| KubeEvents_CL<br> &#124;där not(isempty(Namespace_s))<br> &#124;Sortera efter TimeGenerated fall<br> &#124;Rendera tabell | Kubernetes-händelser|
| ContainerImageInventory<br> &#124;Sammanfatta AggregatedValue = antal() efter bild, ImageTag, körs | Avbildningslager | 
| **Välj Visa diagramalternativet**:<br> Perf<br> &#124;där ObjectName == ”K8SContainer” och CounterName == ”cpuUsageNanoCores” &#124; sammanfatta AvgCPUUsageNanoCores = avg(CounterValue) efter bin (TimeGenerated, 30m), instansnamn | Processorprestanda för behållare | 
| **Välj Visa diagramalternativet**:<br> Perf<br> &#124;där ObjectName == ”K8SContainer” och CounterName == ”memoryRssBytes” &#124; sammanfatta AvgUsedRssMemoryBytes = avg(CounterValue) efter bin (TimeGenerated, 30m), instansnamn | Behållare-minne |

## <a name="next-steps"></a>Nästa steg
Azure Monitor för behållare inkluderar inte en fördefinierad uppsättning aviseringar. Granska den [skapa aviseringar med Azure Monitor för behållare](container-insights-alerts.md) att lära dig hur du skapar rekommenderade aviseringar för hög användning av processor och minne för dina DevOps eller operativa processer och procedurer. 