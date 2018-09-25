---
title: Vanliga mått för autoskalning
description: Lär dig vilka mått används ofta för automatisk skalning dina molntjänster, virtuella datorer och Webbappar.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 48c53b1b0c037e6bcfea3be49fdd2110e1e694b3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970710"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor autoskalning vanliga mått
Automatisk skalning med Azure Monitor kan du skala antalet instanser som körs upp eller ned, baserat på dessa data (mått). Det här dokumentet beskriver vanliga mått som du kanske vill använda. Du kan välja mått på resursen att skala genom att i Azure-portalen. Du kan också välja vilka mått som helst från en annan resurs kan skala med.

Automatisk skalning i Azure Monitor gäller endast [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Andra Azure-tjänster använder olika metoder för skalning.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Compute mått för Resource Manager-baserade virtuella datorer
Som standard Resource Manager-baserade virtuella datorer och VM-Skalningsuppsättningar kan du generera basmått (värdnivå). Dessutom, när du konfigurerar insamling av diagnostik för en virtuell Azure-dator och VMSS, genererar Azure-diagnostiktillägget också gäst-OS-prestandaräknare (kallas ofta ”gäst-OS mått”).  Du använder de här måtten i regler för automatisk skalning.

Du kan använda den `Get MetricDefinitions` API/PoSH/CLI för att visa mått som är tillgängliga för VMSS-resursen.

Om du använder VM-skalningsuppsättningar och du inte ser ett viss mått anges så är det sannolikt *inaktiverat* i diagnostiktillägget.

Om ett visst mått inte håller på att samplas eller överföras på hur ofta du vill, kan du uppdatera diagnostikkonfigurationen.

Om båda föregående fallen är sant, granskar [Använd PowerShell för att aktivera Azure Diagnostics i en virtuell dator med Windows](../virtual-machines/windows/ps-extensions-diagnostics.md) om PowerShell för att konfigurera och uppdatera din Azure VM Diagnostics-tillägg om du vill aktivera måttet. Den här artikeln innehåller också en exempelfil diagnostik konfiguration.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Värdmått för Resource Manager-baserade Windows och Linux-datorer
Följande värdnivå mått genereras som standard för virtuella Azure-datorer och VMSS i både Windows och Linux-instanser. De här måtten beskrivs Azure VM, men har samlats in från Virtuella Azure-värd i stället för via agent installeras på den Virtuella gästdatorn. Du kan använda de här måtten i reglerna för automatisk skalning.

- [Värdmått för Resource Manager-baserade Windows och Linux-datorer](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Värdmått för Resource Manager-baserade Windows och Linux VM Scale Sets](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Gäst-OS mått Resource Manager-baserade Windows virtuella datorer
När du skapar en virtuell dator i Azure kan aktiveras diagnostik genom att använda Diagnostics-tillägg. Diagnostiktillägget genererar en uppsättning mått som kommer från inuti den virtuella datorn. Det innebär att du kan skalas automatiskt på mått som inte genereras som standard.

Du kan skapa en lista över mått med hjälp av följande kommando i PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan skapa en avisering för följande mått:

| Måttnamn | Enhet |
| --- | --- |
| \Processor(_Total)\% processortid |Procent |
| \Processor(_Total)\% privilegierad tid |Procent |
| \Processor(_Total)\% användartid |Procent |
| \Processor information (_Total) \Processor frekvens |Antal |
| \System\Processes |Antal |
| \Process (_Total) \Thread antal |Antal |
| \Process (_Total) \Handle antal |Antal |
| \Memory\% allokerade byte som används |Procent |
| \Memory\Tillgängliga byte |Byte |
| \Memory\Committed byte |Byte |
| \Memory\Commit gräns |Byte |
| \Memory\Pool systemminne-byte |Byte |
| \Memory\Pool växlingsbart systemminne-byte |Byte |
| \PhysicalDisk(_Total)\% disk tid |Procent |
| \PhysicalDisk(_Total)\% Diskläsningar tid |Procent |
| \PhysicalDisk(_Total)\% disk-skrivtid |Procent |
| \PhysicalDisk (_Total) \Disk disköverföringar/sek |CountPerSecond |
| \PhysicalDisk (_Total) \Disk Diskläsningar/sek |CountPerSecond |
| \PhysicalDisk (_Total) \Disk Diskskrivningar/sek |CountPerSecond |
| \PhysicalDisk (_Total) \Disk byte/sek |BytesPerSecond |
| \PhysicalDisk (_Total) \Disk-lästa byte/s |BytesPerSecond |
| \PhysicalDisk (_Total) \Disk skrivna byte/sek |BytesPerSecond |
| \Avg \PhysicalDisk (_Total). Diskkölängd |Antal |
| \Avg \PhysicalDisk (_Total). Läs diskkölängd |Antal |
| \Avg \PhysicalDisk (_Total). Diskkölängd för skrivning |Antal |
| \LogicalDisk(_Total)\% ledigt utrymme |Procent |
| \LogicalDisk (_Total) \Free utrymme i MB |Antal |

### <a name="guest-os-metrics-linux-vms"></a>Gäst-OS mått virtuella Linux-datorer
När du skapar en virtuell dator i Azure, är diagnostik aktiverat som standard med hjälp av diagnostiktillägget.

Du kan skapa en lista över mått med hjälp av följande kommando i PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Du kan skapa en avisering för följande mått:

| Måttnamn | Enhet |
| --- | --- |
| \Memory\AvailableMemory |Byte |
| \Memory\PercentAvailableMemory |Procent |
| \Memory\UsedMemory |Byte |
| \Memory\PercentUsedMemory |Procent |
| \Memory\PercentUsedByCache |Procent |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Byte |
| \Memory\PercentAvailableSwap |Procent |
| \Memory\UsedSwap |Byte |
| \Memory\PercentUsedSwap |Procent |
| \Processor\PercentIdleTime |Procent |
| \Processor\PercentUserTime |Procent |
| \Processor\PercentNiceTime |Procent |
| \Processor\PercentPrivilegedTime |Procent |
| \Processor\PercentInterruptTime |Procent |
| \Processor\PercentDPCTime |Procent |
| \Processor\PercentProcessorTime |Procent |
| \Processor\PercentIOWaitTime |Procent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Sekunder |
| \PhysicalDisk\AverageWriteTime |Sekunder |
| \PhysicalDisk\AverageTransferTime |Sekunder |
| \PhysicalDisk\AverageDiskQueueLength |Antal |
| \NetworkInterface\BytesTransmitted |Byte |
| \NetworkInterface\BytesReceived |Byte |
| \NetworkInterface\PacketsTransmitted |Antal |
| \NetworkInterface\PacketsReceived |Antal |
| \NetworkInterface\BytesTotal |Byte |
| \NetworkInterface\TotalRxErrors |Antal |
| \NetworkInterface\TotalTxErrors |Antal |
| \NetworkInterface\TotalCollisions |Antal |

## <a name="commonly-used-web-server-farm-metrics"></a>Vanliga mätvärden från webben (servergrupp)
Du kan också utföra automatisk skalning baserat på vanliga web servermått, till exempel Http-kölängd. Tjänstmåttets namn är **HttpQueueLength**.  I följande avsnitt visas tillgängliga serverstatistik för servergruppen (Webbappar).

### <a name="web-apps-metrics"></a>Web Apps mått
Du kan skapa en lista över mått för Web Apps med hjälp av följande kommando i PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan Avisera om och av de här måtten.

| Måttnamn | Enhet |
| --- | --- |
| CpuPercentage |Procent |
| MemoryPercentage |Procent |
| DiskQueueLength |Antal |
| HttpQueueLength |Antal |
| BytesReceived |Byte |
| BytesSent |Byte |

## <a name="commonly-used-storage-metrics"></a>Vanliga lagringsmått
Du kan skala genom Kölängd för lagring, vilket är antalet meddelanden i kö för lagring. Kölängd för lagring är en särskild måttet och tröskelvärdet är antalet meddelanden per instans. Till exempel inträffar om det finns två instanser och om tröskelvärdet är inställt på 100, skalning när det totala antalet meddelanden i kön är 200. Det kan vara 100 meddelanden per instans, 120 och 80 eller någon annan kombination som lägger till upp till 200 eller mer.

Konfigurera den här inställningen i Azure-portalen i den **inställningar** bladet. Du kan uppdatera autoskalningsinställning i Resource Manager-mallen som du använder för VM scale sets *metricName* som *ApproximateMessageCount* och skicka ID för storage-kö som  *metricResourceUri*.

Med ett klassiskt Lagringskonto till exempel även Autoskala inställningen metricTrigger:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

För ett lagringskonto med (inte klassisk) omfattar i metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Vanliga mått för Service Bus
Du kan skala genom Kölängd för Service Bus, vilket är antalet meddelanden i Service Bus-kö. Kölängd för Service Bus är en särskild måttet och tröskelvärdet är antalet meddelanden per instans. Till exempel inträffar om det finns två instanser och om tröskelvärdet är inställt på 100, skalning när det totala antalet meddelanden i kön är 200. Det kan vara 100 meddelanden per instans, 120 och 80 eller någon annan kombination som lägger till upp till 200 eller mer.

Du kan uppdatera autoskalningsinställning i Resource Manager-mallen som du använder för VM scale sets *metricName* som *ApproximateMessageCount* och skicka ID för storage-kö som  *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Konceptet för resurs-grupp finns inte för Service Bus, men Azure Resource Manager skapas en resursgrupp för standard per region. Resursgruppen är vanligtvis i formatet ”standard - ServiceBus-[region]”. Till exempel ”standard-ServiceBus-EastUS”, ”standard-ServiceBus-WestUS', 'Standard-ServiceBus-AustraliaEast” osv.
>
>
