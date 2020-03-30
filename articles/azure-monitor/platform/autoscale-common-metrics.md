---
title: Automatisk skalning av vanliga mått
description: Ta reda på vilka mätvärden som ofta används för automatisk skalning av molntjänster, virtuella datorer och webbappar.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 2c335168683212337876c963a7cfdb441d0ac69a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845576"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor automatisk skalning av vanliga mått

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Med autoskalning för Azure Monitor kan du skala antalet instanser som körs uppåt eller nedåt, baserat på telemetridata (mått). I det här dokumentet beskrivs vanliga mått som du kanske vill använda. I Azure-portalen kan du välja måttet för resursen att skala efter. Du kan dock också välja ett mått från en annan resurs att skala efter.

Automatisk skalning av Azure Monitor gäller endast för [skalningsuppsättningar för virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/)datorer, [Molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Webbappar](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Andra Azure-tjänster använder olika skalningsmetoder.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Beräkningsmått för Resource Manager-baserade virtuella datorer
Som standard avger Resource Manager-baserade virtuella datorer och skaluppsättningar för virtuella datorer grundläggande (värdnivå). När du konfigurerar insamling av diagnostikdata för en Azure VM och VMSS avger Azure-diagnostiktillägget dessutom prestandaräknare för gäst-OS (allmänt kända som "gäst-OS-mått").  Du använder alla dessa mått i regler för automatisk skalning.

Du kan `Get MetricDefinitions` använda API/PoSH/CLI för att visa de mått som är tillgängliga för VMSS-resursen.

Om du använder vm-skalningsuppsättningar och du inte ser ett visst mått i listan inaktiveras det *troligen* i diagnostiktillägget.

Om ett visst mått inte samplas eller överförs med den frekvens du vill ha kan du uppdatera diagnostikkonfigurationen.

Om något av föregående ärende är sant, granska [använd PowerShell för att aktivera Azure Diagnostics på en virtuell dator som kör Windows](../../virtual-machines/extensions/diagnostics-windows.md) om PowerShell för att konfigurera och uppdatera azure VM Diagnostics-tillägget för att aktivera måttet. Den artikeln innehåller också en exempeldiagnostik konfigurationsfil.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Värdmått för Resource Manager-baserade virtuella Windows- och Linux-datorer
Följande värden på värdnivå avges som standard för Azure VM och VMSS i både Windows- och Linux-instanser. Dessa mått beskriver din virtuella Azure-dator, men samlas in från Azure VM-värden i stället för via agent installerad på gästdatorn. Du kan använda dessa mått i regler för automatisk skalning.

- [Värdmått för Resource Manager-baserade virtuella Windows- och Linux-datorer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Värdmått för Resource Manager-baserade Windows- och Linux VM-skalningsuppsättningar](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Gästoperativsystemmått för Resource Manager-baserade virtuella Windows-datorer
När du skapar en virtuell dator i Azure aktiveras diagnostik med hjälp av diagnostiktillägget. Tillägget diagnostik avger en uppsättning mått som tas från den virtuella datorn. Det innebär att du kan automatiskt skala bort mått som inte släpps ut som standard.

Du kan generera en lista över måtten med hjälp av följande kommando i PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan skapa en avisering för följande mått:

| Måttnamn | Enhet |
| --- | --- |
| \Processor(_Total)\% processortid |Procent |
| \Processor(_Total)\% privilegierad tid |Procent |
| \Processor(_Total)\% Användartid |Procent |
| \Processorinformation(_Total)\Processorfrekvens |Antal |
| \System\Processer |Antal |
| \Process(_Total)\Antal trådar |Antal |
| \Process(_Total)\Antal handtag |Antal |
| \Minnesupptagna\% byte som används |Procent |
| \Memory\Tillgängliga byte |Byte |
| \Minne\Bekräftade byte |Byte |
| \Memory\Commit-gräns |Byte |
| \Minne\Pool växlingsbyte |Byte |
| \Minne\Pool ej växlingsbara byte |Byte |
| \Fysiskdisk(_Total)\% disktid |Procent |
| \PhysicalDisk(_Total)\% Diskläsningstid |Procent |
| \PhysicalDisk(_Total)\% Diskskrivningstid |Procent |
| \PhysicalDisk(_Total)\Disköverföringar/sek |CountPerSecond |
| \PhysicalDisk(_Total)\Diskläsningar/sek |CountPerSecond |
| \PhysicalDisk(_Total)\Diskskrivningar/sek |CountPerSecond |
| \PhysicalDisk(_Total)\Diskbyten/sek |BytesPerSecond |
| \PhysicalDisk(_Total)\Diskläsningsbyten per sekund |BytesPerSecond |
| \PhysicalDisk(_Total)\Byte för diskskrivning/sek |BytesPerSecond |
| \PhysicalDisk(_Total)\Genomsnittlig diskkölängd |Antal |
| \PhysicalDisk(_Total)\Genomsnittlig kölängd för diskläsning |Antal |
| \PhysicalDisk(_Total)\Genomsnittlig kölängd för diskskrivning |Antal |
| \LogicalDisk(_Total)\% ledigt utrymme |Procent |
| \LogicalDisk(_Total)\Gratis Megabyte |Antal |

### <a name="guest-os-metrics-linux-vms"></a>Gäst-OS-mått Linux virtuella datorer
När du skapar en virtuell dator i Azure aktiveras diagnostik som standard med hjälp av diagnostiktillägg.

Du kan generera en lista över måtten med hjälp av följande kommando i PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Du kan skapa en avisering för följande mått:

| Måttnamn | Enhet |
| --- | --- |
| \Memory\AvailableMemory |Byte |
| \Minne\ProcentTillgängligt Minne |Procent |
| \Minne\AnvändsMinne |Byte |
| \Minne\ProcentAnvändarminne |Procent |
| \Minne\ProcentUsedByCache |Procent |
| \Minne\SidorPerSec |CountPerSecond |
| \Minne\SidorLäsPerSec |CountPerSecond |
| \Minne\PagesWrittenPerSec |CountPerSecond |
| \Minne\TillgängligSwap |Byte |
| \Minne\ProcentTillgängligSwap |Procent |
| \Minne\UsedSwap |Byte |
| \Minne\ProcentUsedSwap |Procent |
| \Processor\PercentIdleTime |Procent |
| \Processor\ProcentAnvändareTime |Procent |
| \Processor\ProcentNiceTime |Procent |
| \Processor\ProcentPrivilegedTime |Procent |
| \Processor\ProcentInterruptTime |Procent |
| \Processor\ProcentDPCTime |Procent |
| \Processor\ProcentProcessorTime |Procent |
| \Processor\ProcentIOWaitTime |Procent |
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
| \NetworkInterface\TotaltKollisioner |Antal |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Vanliga mätvärden för App Service (Server Farm)
Du kan också utföra automatisk skalning baserat på vanliga webbservermått, till exempel Http-kölängden. Dess metriska namn är **HttpQueueLength**.  I följande avsnitt visas tillgängliga servergruppsmått (App Service).

### <a name="web-apps-metrics"></a>Mätvärden för Webbappar
Du kan skapa en lista över web apps-måtten med hjälp av följande kommando i PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan avisera eller skala efter dessa mått.

| Måttnamn | Enhet |
| --- | --- |
| CpuPercentage (processorpercentage) |Procent |
| MinnePercentage |Procent |
| DiskQueueLength |Antal |
| Mer från HttpQueueLength |Antal |
| ByteKom med |Byte |
| BytesSent |Byte |

## <a name="commonly-used-storage-metrics"></a>Vanliga lagringsmått
Du kan skala efter lagringskölängd, vilket är antalet meddelanden i lagringskön. Lagringskölängd är ett speciellt mått och tröskelvärdet är antalet meddelanden per instans. Om det till exempel finns två instanser och om tröskelvärdet är satt till 100, uppstår skalning när det totala antalet meddelanden i kön är 200. Det kan vara 100 meddelanden per instans, 120 och 80 eller någon annan kombination som uppgår till 200 eller mer.

Konfigurera den här inställningen i Azure-portalen i bladet **Inställningar.** För vm-skalningsuppsättningar kan du uppdatera inställningen Automatisk skalning i resource manager-mallen för att använda *metricName* som *ApproximateMessageCount* och skicka ID:t för lagringskön som *metricResourceUri*.

Med ett klassiskt lagringskonto skulle till exempel den automatiska skalningsinställningen metricTrigger innehålla:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

För ett (icke-klassiskt) lagringskonto skulle metricTrigger innehålla:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Vanliga servicebussmått
Du kan skala efter kölängd för Service Bus, vilket är antalet meddelanden i servicebusskön. Service Bus kö längd är ett speciellt mått och tröskelvärdet är antalet meddelanden per instans. Om det till exempel finns två instanser och om tröskelvärdet är satt till 100, uppstår skalning när det totala antalet meddelanden i kön är 200. Det kan vara 100 meddelanden per instans, 120 och 80 eller någon annan kombination som uppgår till 200 eller mer.

För vm-skalningsuppsättningar kan du uppdatera inställningen Automatisk skalning i resource manager-mallen för att använda *metricName* som *ApproximateMessageCount* och skicka ID:t för lagringskön som *metricResourceUri*.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> För Service Bus finns inte resursgruppskonceptet, men Azure Resource Manager skapar en standardresursgrupp per region. Resursgruppen är vanligtvis i formatet "Default-ServiceBus-[region]". Till exempel "Default-ServiceBus-EastUS", "Default-ServiceBus-WestUS", "Default-ServiceBus-AustraliaEast" etc.
>
>

