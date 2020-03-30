---
title: Aviseringar från Azure Monitor för virtuella datorer
description: Beskriver hur du skapar varningsregler från prestandadata som samlas in av Azure Monitor för virtuella datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289607"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Så här skapar du aviseringar från Azure Monitor för virtuella datorer
[Aviseringar i Azure Monitor](../platform/alerts-overview.md) meddelar dig proaktivt om intressanta data och mönster i dina övervakningsdata. Azure Monitor för virtuella datorer innehåller inte förkonfigurerade varningsregler, men du kan skapa dina egna baserat på data som samlas in. Den här artikeln innehåller vägledning om hur du skapar varningsregler, inklusive en uppsättning exempelfrågor.


## <a name="alert-rule-types"></a>Typer av aviseringsregel
Azure Monitor har [olika typer av varningsregler](../platform/alerts-overview.md#what-you-can-alert-on) baserat på de data som används för att skapa aviseringen. Alla data som samlas in av Azure Monitor för virtuella datorer lagras i Azure Monitor Logs som stöder [loggaviseringar](../platform/alerts-log.md). Du kan för närvarande inte använda [måttaviseringar](../platform/alerts-log.md) med prestandadata som samlats in från Azure Monitor för virtuella datorer eftersom data inte samlas in i Azure Monitor Metrics. Om du vill samla in data för måttaviseringar installerar du [diagnostiktillägget](../platform/diagnostics-extension-overview.md) för virtuella Datorer i Windows eller [Telegraf-agenten](../platform/collect-custom-metrics-linux-telegraf.md) för virtuella Linux-datorer för att samla in prestandadata i mått.

Det finns två typer av loggaviseringar i Azure Monitor:

- [Antal resultataviseringar](../platform/alerts-unified-log.md#number-of-results-alert-rules) skapar en enda avisering när en fråga returnerar minst ett angivet antal poster. Dessa är idealiska för icke-numeriska data och Windows- och Syslog-händelser som samlas in av [Log Analytics-agenten](../platform/log-analytics-agent.md) eller för att analysera prestandatrender på flera datorer.
- [Måttmätningsvarsaviseringar](../platform/alerts-unified-log.md#metric-measurement-alert-rules) skapar en separat avisering för varje post i en fråga som har ett värde som överskrider ett tröskelvärde som definieras i varningsregeln. Dessa varningsregler är idealiska för prestandadata som samlas in av Azure Monitor för virtuella datorer eftersom de kan skapa enskilda aviseringar för varje dator.


## <a name="alert-rule-walkthrough"></a>Genomgång av varningsregel
Det här avsnittet går igenom skapandet av en måttmätningsaviseringsregel med hjälp av prestandadata från Azure Monitor för virtuella datorer. Du kan använda den här grundläggande processen med en mängd olika loggfrågor för att avisera olika prestandaräknare.

Börja med att skapa en ny aviseringsregel enligt proceduren i [Skapa, visa och hantera loggaviseringar med Azure Monitor](../platform/alerts-log.md). För **resursen**väljer du arbetsytan Log Analytics som Azure Monitor virtuella datorer använder i din prenumeration. Eftersom målresursen för loggaviseringsregler alltid är en Log Analytics-arbetsyta måste loggfrågan innehålla alla filter för vissa virtuella datorer eller skaluppsättningar för virtuella datorer eller virtuella datorer. 

Använd en **av** frågorna i avsnittet [nedan](#sample-alert-queries) som **sökfråga**för varningsregeln . Frågan måste returnera en numerisk egenskap som kallas *AggregatedValue*. Den bör sammanfatta data efter dator så att du kan skapa en separat avisering för varje virtuell dator som överskrider tröskelvärdet.

Välj **Måttmätning** **i varningslogiken**och ange sedan ett **tröskelvärde**. I **Trigger Alert Baserat på**anger du hur många gånger tröskelvärdet måste överskridas innan en avisering skapas. Du bryr dig till exempel inte om processorn överskrider ett tröskelvärde en gång och sedan återgår till det normala, men du bryr dig om den fortsätter att överskrida tröskelvärdet över flera mätningar i följd.

Avsnittet **Utvärderat baserat på** definierar hur ofta frågan körs och tidsfönstret för frågan. I exemplet nedan körs frågan var 15:e minut och utvärderar prestandavärden som samlats in under de senaste 15 minuterna.


![Varningsregel för måttmätning](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Exempel på aviseringsfrågor
Följande frågor kan användas med en måttmätningsaviseringsregel med hjälp av prestandadata som samlas in av Azure Monitor för virtuella datorer. Varje summerar data per dator så att en avisering skapas för varje dator med ett värde som överskrider tröskelvärdet.

### <a name="cpu-utilization"></a>CPU-användning

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Tillgängligt minne i MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Tillgängligt minne i procent

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Logisk disk som används - alla diskar på varje dator

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Logisk disk som används - enskilda diskar

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>Logisk disk IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Valutakurs för logiska diskdata

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Nätverksgränssnitt mottagna byte - alla gränssnitt

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Nätverksgränssnitt mottagna byte - enskilda gränssnitt

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Nätverksgränssnitt byte skickas - alla gränssnitt

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Nätverksgränssnitt byte skickas - enskilda gränssnitt

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Skaluppsättning för virtuella datorer
Ändra med ditt prenumerations-ID, resursgrupp och skaluppsättningsnamn för virtuella datorer.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Specifik virtuell dator
Ändra med ditt prenumerations-ID, resursgrupp och VM-namn.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>CPU-användning för alla beräkningsresurser i en prenumeration
Ändra med ditt prenumerations-ID.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>CPU-användning för alla beräkningsresurser i en resursgrupp
Ändra med ditt prenumerations-ID och resursgrupp.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [aviseringar i Azure Monitor](../platform/alerts-overview.md).
- Läs mer om [loggfrågor med data från Azure Monitor för virtuella datorer](vminsights-log-search.md).
