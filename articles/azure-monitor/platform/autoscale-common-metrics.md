---
title: Autoskala vanliga mått
description: Lär dig vilka mått som används ofta för automatisk skalning av Cloud Services, Virtual Machines och Web Apps.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 9da8e5fb88ff34e561b579b760973ecd23c884a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "66129737"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor vanliga mått för autoskalning

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Med Azure Monitor autoskalning kan du skala upp eller ned antalet instanser som körs, baserat på telemetridata (mått). Det här dokumentet beskriver vanliga mått som du kanske vill använda. I Azure Portal kan du välja måttet för den resurs som ska skalas efter. Du kan dock också välja mått från en annan resurs som ska skalas efter.

Azure Monitor autoskalning gäller endast för [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)-, [Cloud Services](https://azure.microsoft.com/services/cloud-services/)-, [App Service-Web Apps-](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Andra Azure-tjänster använder olika skalnings metoder.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Beräknings mått för Resource Manager-baserade virtuella datorer
Som standard, Resource Manager-baserad Virtual Machines och Virtual Machine Scale Sets genererar grundläggande mått (värdnivå). När du konfigurerar diagnostik-datainsamling för en virtuell Azure-dator och VMSS, genererar dessutom Azure Diagnostic-tillägget även gäst-OS-prestandaräknare (kallas vanligt vis "gäst-OS-mått").  Du kan använda alla dessa mått i regler för autoskalning.

Du kan använda `Get MetricDefinitions` API/PoSH/CLI för att visa de mått som är tillgängliga för din VMSS-resurs.

Om du använder skalnings uppsättningar för virtuella datorer och du inte ser ett visst mått i listan, är det förmodligen inaktiverat i tillägget för diagnostik.

Om ett visst mått inte inhämtas eller överförs enligt den frekvens du vill ha, kan du uppdatera konfigurationen för diagnostik.

Om något av föregående fall är sant kan du läsa igenom [Använd PowerShell för att aktivera Azure-diagnostik på en virtuell dator som kör Windows](../../virtual-machines/extensions/diagnostics-windows.md) PowerShell för att konfigurera och uppdatera tillägget för Azure VM-diagnostik för att aktivera måttet. Artikeln innehåller även en exempel på en diagnostisk konfigurations fil.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Värd mått för Resource Manager-baserade virtuella Windows-och Linux-datorer
Följande mått på värdnivå genereras som standard för virtuella Azure-datorer och VMSS i både Windows-och Linux-instanser. Dessa mått beskriver din virtuella Azure-dator, men samlas in från Azure VM-värden i stället för via agent som är installerad på den virtuella gäst datorn. Du kan använda dessa mått i regler för automatisk skalning.

- [Värd mått för Resource Manager-baserade virtuella Windows-och Linux-datorer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Värd mått för Resource Manager-baserade Windows-och Linux-VM Scale Sets](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Gäst operativ system mått Resource Manager-baserade virtuella Windows-datorer
När du skapar en virtuell dator i Azure aktive ras diagnostik med hjälp av tillägget Diagnostics. Tillägget Diagnostics ger en uppsättning mått som tas från den virtuella datorn. Det innebär att du kan Autoskala av mått som inte genereras som standard.

Du kan skapa en lista över måtten med hjälp av följande kommando i PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan skapa en avisering för följande mått:

| Måttnamn | Enhet |
| --- | --- |
| \Processor(_Total)\% processortid |Percent |
| \Processor (_ total\% ) privilegie rad tid |Percent |
| \Processor (_ total\% ) användar tid |Percent |
| \Processor information (_ total) \Processor frekvens |Count |
| \System\Processes |Count |
| \Process (_ total) \Thread antal |Count |
| \Process (_ total) \Handle antal |Count |
| \Memory\% allokerade byte som används |Percent |
| \Memory\Tillgängliga byte |Byte |
| \Memory\Committed byte |Byte |
| \Memory\Commit-gräns |Byte |
| \Memory\Pool växlade byte |Byte |
| \Memory\Pool byte som inte har Pages |Byte |
| \PhysicalDisk (_ total\% ), disk tid |Percent |
| Läs tid för \PhysicalDisk\% (_ total) |Percent |
| Skriv tid för \PhysicalDisk\% (_ total) |Percent |
| \PhysicalDisk (_ total) \ disk överföringar/SEK |CountPerSecond |
| \PhysicalDisk (_ total) \ disk läsningar/s |CountPerSecond |
| \PhysicalDisk (_ total) \ disk skrivningar/s |CountPerSecond |
| \PhysicalDisk (_ total) \ Disk byte/s |BytesPerSecond |
| \PhysicalDisk (_ total) \ disk lästa byte/s |BytesPerSecond |
| \PhysicalDisk (_ total) \ disk skrivna byte/s |BytesPerSecond |
| \PhysicalDisk (_ total) \Avg. Diskkölängd |Count |
| \PhysicalDisk (_ total) \Avg. Längd på disk läsnings kön |Count |
| \PhysicalDisk (_ total) \Avg. Längd på disk skrivnings kön |Count |
| \Logisk disk (totalt)\% ledigt utrymme |Percent |
| \Logisk disk (_ total) \Ledigt megabyte |Count |

### <a name="guest-os-metrics-linux-vms"></a>Gäst operativ system mått virtuella Linux-datorer
När du skapar en virtuell dator i Azure aktive ras diagnostik som standard med hjälp av tillägget Diagnostics.

Du kan skapa en lista över måtten med hjälp av följande kommando i PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Du kan skapa en avisering för följande mått:

| Måttnamn | Enhet |
| --- | --- |
| \Memory\AvailableMemory |Byte |
| \Memory\PercentAvailableMemory |Percent |
| \Memory\UsedMemory |Byte |
| \Memory\PercentUsedMemory |Percent |
| \Memory\PercentUsedByCache |Percent |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Byte |
| \Memory\PercentAvailableSwap |Percent |
| \Memory\UsedSwap |Byte |
| \Memory\PercentUsedSwap |Percent |
| \Processor\PercentIdleTime |Percent |
| \Processor\PercentUserTime |Percent |
| \Processor\PercentNiceTime |Percent |
| \Processor\PercentPrivilegedTime |Percent |
| \Processor\PercentInterruptTime |Percent |
| \Processor\PercentDPCTime |Percent |
| \Processor\PercentProcessorTime |Percent |
| \Processor\PercentIOWaitTime |Percent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Sekunder |
| \PhysicalDisk\AverageWriteTime |Sekunder |
| \PhysicalDisk\AverageTransferTime |Sekunder |
| \PhysicalDisk\AverageDiskQueueLength |Count |
| \NetworkInterface\BytesTransmitted |Byte |
| \NetworkInterface\BytesReceived |Byte |
| \NetworkInterface\PacketsTransmitted |Count |
| \NetworkInterface\PacketsReceived |Count |
| \NetworkInterface\BytesTotal |Byte |
| \NetworkInterface\TotalRxErrors |Count |
| \NetworkInterface\TotalTxErrors |Count |
| \NetworkInterface\TotalCollisions |Count |

## <a name="commonly-used-web-server-farm-metrics"></a>Vanliga mått för webb Server grupper
Du kan också utföra autoskalning baserat på vanliga webb server mått, till exempel http-köns längd. IT-måttets namn är **HttpQueueLength**.  I följande avsnitt visas tillgängliga mått för Server grupp (Web Apps).

### <a name="web-apps-metrics"></a>Web Apps mått
Du kan generera en lista med Web Apps måtten med hjälp av följande kommando i PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan varna för eller skala med dessa mått.

| Måttnamn | Enhet |
| --- | --- |
| CpuPercentage |Percent |
| MemoryPercentage |Percent |
| DiskQueueLength |Count |
| HttpQueueLength |Count |
| BytesReceived |Byte |
| Bytes sent |Byte |

## <a name="commonly-used-storage-metrics"></a>Ofta använda lagrings mått
Du kan skala efter lagrings köns längd, vilket är antalet meddelanden i lagrings kön. Längden på lagrings kön är ett speciellt mått och tröskelvärdet är antalet meddelanden per instans. Om det till exempel finns två instanser och om tröskelvärdet är inställt på 100 uppstår skalning när det totala antalet meddelanden i kön är 200. Det kan vara 100 meddelanden per instans, 120 och 80, eller någon annan kombination som lägger till upp till 200 eller mer.

Konfigurera den här inställningen i Azure Portal på bladet **Inställningar** . För VM Scale-uppsättningar kan du uppdatera inställningen för autoskalning i Resource Manager-mallen för att använda *metricName* som *ApproximateMessageCount* och skicka ID: t för lagrings kön som *metricResourceUri*.

Till exempel, med ett klassiskt lagrings konto, inkluderar metricTrigger för autoskalning följande:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

För ett (icke-klassiskt) lagrings konto skulle metricTrigger innehålla:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Ofta använda Service Bus mått
Du kan skala efter Service Bus Kölängd, vilket är antalet meddelanden i Service Bus kön. Service Bus Kölängd är ett särskilt mått och tröskelvärdet är antalet meddelanden per instans. Om det till exempel finns två instanser och om tröskelvärdet är inställt på 100 uppstår skalning när det totala antalet meddelanden i kön är 200. Det kan vara 100 meddelanden per instans, 120 och 80, eller någon annan kombination som lägger till upp till 200 eller mer.

För VM Scale-uppsättningar kan du uppdatera inställningen för autoskalning i Resource Manager-mallen för att använda *metricName* som *ApproximateMessageCount* och skicka ID: t för lagrings kön som *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> För Service Bus finns inte det här konceptet för resurs grupp, men Azure Resource Manager skapar en standard resurs grupp per region. Resurs gruppen är vanligt vis i formatet default-Service Bus-[region]. Till exempel "default-Service Bus-öster", "default-Service Bus-väst", "default-Service Bus-AustraliaEast" osv.
>
>

