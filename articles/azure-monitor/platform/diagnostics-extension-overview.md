---
title: Översikt över Azure Diagnostics-tillägg
description: Använd Azure-diagnostik för felsökning, mätning av prestanda, övervakning, trafikanalys i molntjänster, virtuella datorer och tjänstinfrastruktur
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672386"
---
# <a name="azure-diagnostics-extension-overview"></a>Översikt över Azure Diagnostics-tillägg
Azure Diagnostics-tillägget är en [agent i Azure Monitor](agents-overview.md) som samlar in övervakningsdata från gästoperativsystemet för Azure-beräkningsresurser inklusive virtuella datorer. Den här artikeln innehåller en översikt över Azure Diagnostics-tillägget, inklusive specifika funktioner som den stöder och alternativ för installation och konfiguration. 

> [!NOTE]
> Azure Diagnostics-tillägget är en av de agenter som är tillgängliga för att samla in övervakningsdata från gästoperativsystemet med beräkningsresurser. Se [Översikt över Azure Monitor-agenter](agents-overview.md) för en beskrivning av de olika agenterna och vägledning om hur du väljer lämpliga agenter för dina behov.

## <a name="comparison-to-log-analytics-agent"></a>Jämförelse med Log Analytics-agent
Log Analytics-agenten i Azure Monitor kan också användas för att samla in övervakningsdata från gästoperativsystemet för virtuella datorer. Du kan välja att använda antingen eller båda beroende på dina krav. Se [Översikt över Azure Monitor-agenter](agents-overview.md) för en detaljerad jämförelse av Azure Monitor-agenter. 

De viktigaste skillnaderna att tänka på är:

- Azure Diagnostics Extension kan endast användas med virtuella Azure-datorer. Log Analytics-agenten kan användas med virtuella datorer i Azure, andra moln och lokalt.
- Azure Diagnostics-tillägget skickar data till Azure Storage, [Azure Monitor Metrics](data-platform-metrics.md) (endast Windows) och Event Hubs. Log Analytics-agenten samlar in data till [Azure Monitor Logs](data-platform-logs.md).
- Log Analytics-agenten krävs för [lösningar,](../monitor-reference.md#insights-and-core-solutions) [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md)och andra tjänster som Azure Security [Center](/azure/security-center/).

## <a name="costs"></a>Kostnader
Det finns ingen kostnad för Azure Diagnostic Extension, men du kan debiteras avgifter för de data som förtärs. Kontrollera [Azure Monitor-priser](https://azure.microsoft.com/pricing/details/monitor/) för målet där du samlar in data.

## <a name="data-collected"></a>Insamlade data
I följande tabeller visas de data som kan samlas in av tillägget Windows- och Linux-diagnostik.

### <a name="windows-diagnostics-extension-wad"></a>Windows-diagnostiktillägg (WAD)

| Datakälla | Beskrivning |
| --- | --- |
| Windows-händelseloggar   | Händelser från Windows-händelselogg. |
| Prestandaräknare | Numeriska värden som mäter prestanda för olika aspekter av operativsystem och arbetsbelastningar. |
| IIS-loggar             | Användningsinformation för IIS-webbplatser som körs på gästoperativsystemet. |
| Programloggar     | Spåra meddelanden skrivna av ditt program. |
| .NET EventSource-loggar |Kodskrivningshändelser med klassen .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| [Manifestbaserade ETW-loggar](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Händelsespårning för Windows-händelser som genereras av alla processer. |
| Kraschdumpar (loggar)   | Information om processens tillstånd om ett program kraschar. |
| Filbaserade loggar    | Loggar som skapats av ditt program eller din tjänst. |
| Agent diagnostiska loggar | Information om Azure Diagnostics själv. |


### <a name="linux-diagnostics-extension-lad"></a>Linux diagnostik förlängning (LAD)

| Datakälla | Beskrivning |
| --- | --- |
| Syslog | Händelser som skickas till Linux-händelseloggningssystemet.   |
| Prestandaräknare  | Numeriska värden som mäter prestanda för olika aspekter av operativsystem och arbetsbelastningar. |
| Loggfiler | Poster som skickas till en filbaserad logg.  |

## <a name="data-destinations"></a>Destinationer för data
Azure Diagnostic-tillägget för både Windows och Linux samlar alltid in data i ett Azure Storage-konto. Se [Installera och konfigurera Windows Azure diagnostics extension (WAD)](diagnostics-extension-windows-install.md) och Använd Linux Diagnostic Extension för att övervaka mått och [loggar](../../virtual-machines/extensions/diagnostics-linux.md) för en lista över specifika tabeller och blobbar där dessa data samlas in.

Konfigurera en eller flera *datamottagare* för att skicka data till andra ytterligare mål. I följande avsnitt listas de sänkor som är tillgängliga för tillägget Windows- och Linux-diagnostik.

### <a name="windows-diagnostics-extension-wad"></a>Windows-diagnostiktillägg (WAD)

| Mål | Beskrivning |
|:---|:---|
| Azure-övervakarmått | Samla in prestandadata till Azure Monitor Metrics. Se [Skicka gästoperativsystem-mått till Azure Monitor-måttdatabasen](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Händelsehubbar | Använd Azure Event Hubs för att skicka data utanför Azure. Se [Strömmande Azure Diagnostics-data till eventhubbar](diagnostics-extension-stream-event-hubs.md) |
| Azure Storage-blobbar | Skriv till data för att blobbar i Azure Storage förutom tabeller. |
| Application Insights | Samla in data från program som körs i den virtuella datorn till Application Insights för att integrera med annan programövervakning. Se [Skicka diagnostikdata till Application Insights](diagnostics-extension-to-application-insights.md). |

Du kan också samla in WAD-data från lagring till en Log Analytics-arbetsyta för att analysera dem med Azure Monitor Logs, även om Log Analytics-agenten vanligtvis används för den här funktionen. Den kan skicka data direkt till en Log Analytics-arbetsyta och stöder lösningar och insikter som ger ytterligare funktioner.  Se [Samla in Azure-diagnostikloggar från Azure Storage](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Linux diagnostik förlängning (LAD)
LAD skriver data till tabeller i Azure Storage. Den stöder sänkorna i följande tabell.

| Mål | Beskrivning |
|:---|:---|
| Händelsehubbar | Använd Azure Event Hubs för att skicka data utanför Azure. |
| Azure Storage-blobbar | Skriv till data för att blobbar i Azure Storage förutom tabeller. |
| Azure-övervakarmått | Installera Telegraf-agenten utöver LAD. Se [Samla in anpassade mått för en virtuell Linux-dator med Agenten InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Installation och konfiguration
Diagnostiktillägget implementeras som ett [tillägg för virtuella datorer](../../virtual-machines/extensions/overview.md) i Azure, så det stöder samma installationsalternativ med Hjälp av Resource Manager-mallar, PowerShell och CLI. Se [Tillägg och funktioner för virtuella](../../virtual-machines/extensions/features-windows.md) datorer för Windows och virtuella [datortillägg och funktioner för Linux](../../virtual-machines/extensions/features-linux.md) för allmän information om hur du installerar och underhåller tillägg för virtuella datorer.

Du kan också installera och konfigurera både Windows- och Linux-diagnostiktillägget i Azure-portalen under **Diagnostikinställningar** i avsnittet **Övervakning** på den virtuella datorns meny.

Mer information om hur du installerar och konfigurerar diagnostiktillägget för Windows och Linux finns i följande artiklar.

- [Installera och konfigurera Windows Azure diagnostics-tillägg (WAD)](diagnostics-extension-windows-install.md)
- [Använda Linux-diagnostiktillägget för att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Annan dokumentation

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service (klassiska) webb- och arbetsroller
- [Introduktion till övervakning av molntjänster](../../cloud-services/cloud-services-how-to-monitor.md)
- [Aktivera Azure Diagnostics i Azure Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Programinsikter för Azure-molntjänster](../app/cloudservices.md)<br>[Spåra flödet för ett Cloud Services-program med Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Övervaka och diagnostisera tjänster i en konfiguration för utveckling lokalt](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Nästa steg


* Lär dig att [använda prestandaräknare i Azure Diagnostics](../../cloud-services/diagnostics-performance-counters.md).
* Om du har problem med diagnostik som startar eller hittar dina data i Azure-lagringstabeller läser [du Felsöka Azure Diagnostics](diagnostics-extension-troubleshooting.md)

