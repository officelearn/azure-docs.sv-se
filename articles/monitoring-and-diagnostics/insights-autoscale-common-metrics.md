---
title: Autoskala vanliga mått
description: Information om vilka mått används ofta för autoskalning dina molntjänster, virtuella datorer och Web Apps.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 7b6f454a8d4c8794b8c56494fd9ed573f8b79852
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262247"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure övervakaren autoskalning vanliga mått
Azure övervakaren autoskalning kan du skala antalet instanser upp eller ned, enligt telemetridata (mått). Det här dokumentet beskriver vanliga mått som du kanske vill använda. Du kan välja mått av resursen ska skala med i Azure-portalen för molntjänster och servergrupper. Du kan också välja alla mått från skala med en annan resurs.

Azure övervakaren Autoskala gäller enbart för [Skalningsuppsättningar i virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [molntjänster](https://azure.microsoft.com/services/cloud-services/), och [App Service - Webbappar](https://azure.microsoft.com/services/app-service/web/). Andra Azure-tjänster använda olika metoder för skalning.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Beräkna mått för Resource Manager-baserade virtuella datorer
Som standard genererar grundläggande (värdnivå) mått av Resource Manager-baserade virtuella datorer och virtuella datorer. Dessutom, när du konfigurerar diagnostik datainsamling för en virtuell dator i Azure och VMSS avger Azure-diagnostik tillägget också gäst-OS prestandaräknare (kallas ofta ”gäst-OS mått”).  Du kan använda de här måtten i Autoskala regler.

Du kan använda den `Get MetricDefinitions` API/PoSH/CLI för att visa mätvärdena som är tillgängliga för din VMSS resurs.

Om du använder skalningsuppsättningar och du inte ser ett viss mått i listan och sedan är förmodligen *inaktiverat* i diagnostik-tillägget.

Om inte används för ett viss mått provtagning eller överföras på hur ofta du vill du uppdatera konfigurationen av diagnostik.

Om antingen föregående fall är true, granska [Använd PowerShell för att aktivera Azure-diagnostik i en virtuell dator som kör Windows](../virtual-machines/windows/ps-extensions-diagnostics.md) om PowerShell för att konfigurera och uppdatera din Azure-diagnostik för VM-tillägget om du vill aktivera mått. Artikeln innehåller också en exempelfil diagnostik konfiguration.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Värden mått för Resource Manager-baserade Windows och Linux virtuella datorer
Följande värdnivå mått släpps som standard för virtuella Azure-datorn och VMSS i Windows- och Linux-instanser. De här måtten beskriver Azure VM, men har samlats in från den Virtuella Azure-värden i stället för via agenten är installerad på den Virtuella gästdatorn. Du kan använda de här måtten i autoskalning regler.

- [Värden mått för Resource Manager-baserade Windows och Linux virtuella datorer](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Värden mått för Resource Manager-baserade Windows och Linux-Skalningsuppsättningar](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Gästoperativsystem mått Resource Manager-baserade virtuella Windows-datorer
När du skapar en virtuell dator i Azure aktiveras diagnostics genom att använda tillägget diagnostik. Diagnostik-tillägget skickar en uppsättning mått som hämtats från inuti den virtuella datorn. Detta innebär att du kan Autoskala från mått som inte har orsakat som standard.

Du kan generera en lista över mätvärdena som med hjälp av följande kommando i PowerShell.

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
| \Memory\% använda dedikerade byte |Procent |
| \Memory\Tillgängliga byte |Byte |
| \Memory\Committed byte |Byte |
| \Memory\Commit gräns |Byte |
| \Memory\Pool systemminne-byte |Byte |
| \Memory\Pool växlingsbart systemminne-byte |Byte |
| \PhysicalDisk(_Total)\% disk tid |Procent |
| \PhysicalDisk(_Total)\% disk Lästid |Procent |
| \PhysicalDisk(_Total)\% disk skrivåtgärder tid |Procent |
| \PhysicalDisk (_Total) \Disk disköverföringar/sek |CountPerSecond |
| \PhysicalDisk (_Total) \Disk Diskläsningar/sek |CountPerSecond |
| \PhysicalDisk (_Total) \Disk Diskskrivningar/sek |CountPerSecond |
| \PhysicalDisk (_Total) \Disk byte/sek |BytesPerSecond |
| \PhysicalDisk (_Total) \Disk-lästa byte/s |BytesPerSecond |
| \PhysicalDisk (_Total) \Disk skrivna byte/s |BytesPerSecond |
| \Avg \PhysicalDisk (_Total). Diskkölängd |Antal |
| \Avg \PhysicalDisk (_Total). Läs diskkölängd |Antal |
| \Avg \PhysicalDisk (_Total). Diskkölängd för skrivning |Antal |
| \LogicalDisk(_Total)\% ledigt utrymme |Procent |
| \LogicalDisk (_Total) \Free megabyte |Antal |

### <a name="guest-os-metrics-linux-vms"></a>Gästoperativsystem mått virtuella Linux-datorer
När du skapar en virtuell dator i Azure är diagnostics aktiverad som standard med hjälp av diagnostik-tillägget.

Du kan generera en lista över mätvärdena som med hjälp av följande kommando i PowerShell.

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

## <a name="commonly-used-web-server-farm-metrics"></a>Vanliga mätvärden från webben (servergruppen)
Du kan också utföra Autoskala baserat på gemensamma web-serverstatistik som Kölängd för Http. Tjänstmåttets namn är **HttpQueueLength**.  I följande avsnitt visas tillgängliga serverstatistik för servergruppen (webbprogram).

### <a name="web-apps-metrics"></a>Web Apps mått
Du kan generera en lista över mätvärdena som Web Apps med hjälp av följande kommando i PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan varning i eller skala av de här måtten.

| Måttnamn | Enhet |
| --- | --- |
| CpuPercentage |Procent |
| MemoryPercentage |Procent |
| DiskQueueLength |Antal |
| HttpQueueLength |Antal |
| BytesReceived |Byte |
| BytesSent |Byte |

## <a name="commonly-used-storage-metrics"></a>Vanliga Storage-mätvärden
Du kan skala av lagring Kölängd, är antalet meddelanden i kö för lagring. Kölängd för lagring är en särskild mått och tröskelvärdet är antalet meddelanden per instans. Till exempel inträffar om det finns två instanser och tröskelvärdet är satt till 100 skalning när det totala antalet meddelanden i kön är 200. Som kan vara 100 meddelanden per instans, 120 och 80, eller någon annan kombination som lägger till upp till 200 eller mer.

Konfigurera den här inställningen i Azure-portalen i den **inställningar** bladet. För skalningsuppsättningar, kan du uppdatera autoskalningsinställning i Resource Manager-mall att använda *metricName* som *ApproximateMessageCount* och ange ID för storage-kö som *metricResourceUri*.

Med ett klassiska Lagringskonto till exempel även Autoskala inställningen metricTrigger:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

För ett lagringskonto (icke-klassisk) omfattar metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Vanliga Service Bus-mått
Du kan skala av Service Bus Kölängd, är antalet meddelanden i Service Bus-kö. Kölängd för Service Bus är en särskild mått och tröskelvärdet är antalet meddelanden per instans. Till exempel inträffar om det finns två instanser och tröskelvärdet är satt till 100 skalning när det totala antalet meddelanden i kön är 200. Som kan vara 100 meddelanden per instans, 120 och 80, eller någon annan kombination som lägger till upp till 200 eller mer.

För skalningsuppsättningar, kan du uppdatera autoskalningsinställning i Resource Manager-mall att använda *metricName* som *ApproximateMessageCount* och ange ID för storage-kö som *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Begreppet resurs gruppen finns inte för Service Bus, men Azure Resource Manager skapar en standard resursgrupp per region. Resursgruppen är vanligtvis i formatet 'Default - ServiceBus-[region]'. Till exempel 'Standard-ServiceBus-EastUS', 'Standard-ServiceBus-WestUS', 'Standard-ServiceBus-AustraliaEast ”osv.
>
>
