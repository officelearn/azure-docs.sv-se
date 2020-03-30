---
title: Översikt över Azure-övervakningsagenter| Microsoft-dokument
description: Den här artikeln innehåller en detaljerad översikt över de Azure-agenter som är tillgängliga som stöder övervakning av virtuella datorer som finns i Azure eller hybridmiljö.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249092"
---
# <a name="overview-of-azure-monitor-agents"></a>Översikt över Azure Monitor-agenter

Virtuella datorer och andra beräkningsresurser kräver att en agent samlar in övervakningsdata för att mäta prestanda och tillgänglighet för deras gästoperativsystem och arbetsbelastningar. I den här artikeln beskrivs de agenter som används av Azure Monitor och hjälper dig att avgöra vilka du behöver för att uppfylla kraven för just din miljö.

> [!NOTE]
> Azure Monitor har för närvarande flera agenter på grund av den senaste konsolideringen av Azure Monitor och Log Analytics. Även om det kan finnas överlappningar i deras funktioner, har var och en unika funktioner. Beroende på dina behov kan du behöva en eller flera agenter på dina virtuella datorer. 

Du kan ha en specifik uppsättning krav som inte helt kan uppfyllas med en enda agent för en viss virtuell dator. Du kanske till exempel vill använda måttaviseringar som kräver Azure diagnostics-tillägg men också vill utnyttja funktionerna i Azure Monitor för virtuella datorer som kräver Log Analytics-agenten och beroendeagenten. I fall som detta kan du använda flera agenter, och det här är ett vanligt scenario för kunder som behöver funktioner från varje.

## <a name="summary-of-agents"></a>Sammanfattning av agenter

Följande tabeller ger en snabb jämförelse av Azure Monitor-agenter för Windows och Linux. Ytterligare detaljer om varje finns i avsnittet nedan. 

### <a name="windows-agents"></a>Windows-agenter

| | Diagnostik<br>förlängning (WAD) | Log Analytics<br>Agent | Beroende<br>Agent |
|:---|:---|:---|:---|
| Miljöer som stöds | Azure | Azure<br>Annat moln<br>Lokal | Azure<br>Annat moln<br>Lokal | 
| Agent krav  | Inget | Inget | Kräver Log Analytics-agent |
| Insamlade data | Händelseloggar<br>ETW-händelser<br>Prestanda<br>Filbaserade loggar<br>IIS-loggar<br>.NET-apploggar<br>Kraschdumpar<br>Agent diagnostik loggar | Händelseloggar<br>Prestanda<IIS logs><br>Filbaserade loggar<br>Insikter och lösningar<br>Övriga tjänster | Processinformation och beroenden<br>Mått för nätverksanslutning |
| Data som skickas till | Azure Storage<br>Azure-övervakarmått<br>Händelsehubb | Azure Monitor-loggar | Azure Monitor-loggar |


### <a name="linux-agents"></a>Linux-agenter

| | Diagnostik<br>förlängning (LAD) | Telegraf (svenska)<br>Agent | Log Analytics<br>Agent | Beroende<br>Agent |
|:---|:---|:---|:---|:---|
| Miljöer som stöds | Azure | Azure<br>Annat moln<br>Lokal | Azure<br>Annat moln<br>Lokal | Azure<br>Annat moln<br>Lokal |
| Agent krav  | Inget | Inget | Inget | Kräver Log Analytics-agent |
| Insamlade data | Syslog<br>Prestanda | Prestanda | Syslog<br>Prestanda| Processinformation och beroenden<br>Mått för nätverksanslutning |
| Data som skickas till | Azure Storage<br>Händelsehubb | Azure-övervakarmått | Azure Monitor-loggar | Azure Monitor-loggar |

## <a name="log-analytics-agent"></a>Log Analytics-agent

[Log Analytics-agenten](log-analytics-agent.md) samlar in övervakningsdata från gästoperativsystemet och arbetsbelastningar för virtuella datorer i Azure, andra molnleverantörer och lokala. Den samlar in data i en Log Analytics-arbetsyta. Log Analytics-agenten är samma agent som används av System Center Operations Manager och du kan multihome-agentdatorer för att kommunicera med din hanteringsgrupp och Azure Monitor samtidigt. Den här agenten krävs också av vissa insikter och lösningar i Azure Monitor.


> [!NOTE]
> Log Analytics-agenten för Windows kallas ofta för Microsoft Monitoring Agent (MMA). Log Analytics-agenten för Linux kallas ofta OMS-agent.



Använd Log Analytics-agenten om du behöver:

* Samla in loggar och prestandadata från virtuella eller fysiska datorer utanför Azure. 
* Skicka data till en Log Analytics-arbetsyta för att dra nytta av funktioner som stöds av [Azure Monitor Logs,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) till exempel [loggfrågor](../log-query/log-query-overview.md).
* Använd [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) som gör att du kan övervaka dina virtuella datorer i stor skala och övervakar deras processer och beroenden på andra resurser och externa processer..  
* Hantera säkerheten för dina virtuella datorer med [Azure Security Center](../../security-center/security-center-intro.md) eller Azure [Sentinel](../../sentinel/overview.md).
* Använd [Azure Automation Update management,](../../automation/automation-update-management.md)Azure Automation State [Configuration](../../automation/automation-dsc-overview.md)eller Azure Automation Change Tracking [and Inventory](../../automation/change-tracking.md) för att leverera omfattande hantering av dina virtuella Azure-datorer
* Använd olika [lösningar](../monitor-reference.md#insights-and-core-solutions) för att övervaka en viss tjänst eller ett visst program.

Begränsningar för Log Analytics-agenten inkluderar:

- Det går inte att skicka data till Azure Monitor Metrics, Azure Storage eller Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Azure Diagnostics-tillägg

[Azure Diagnostics-tillägget](diagnostics-extension-overview.md) samlar in övervakningsdata från gästoperativsystemet och arbetsbelastningar för virtuella Azure-datorer och andra beräkningsresurser. Den samlar främst in data i Azure Storage men låter dig också definiera datamottagare för att även skicka data till andra destinationer som Azure Monitor Metrics och Azure Event Hubs.

Använd Azure-diagnostiktillägg om du behöver:

- Skicka data till Azure Storage för arkivering eller för att analysera dem med verktyg som [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Skicka data till [Azure Monitor-mått](data-platform-metrics.md) för att analysera dem med [statistikutforskaren](metrics-getting-started.md) och dra nytta av funktioner som [mätaviseringar](../../azure-monitor/platform/alerts-metric-overview.md) i nära realtid och [automatisk skalning](autoscale-overview.md) (endast Windows).
- Skicka data till verktyg från tredje part med [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Samla [boot diagnostics](../../virtual-machines/troubleshooting/boot-diagnostics.md) för att undersöka vm-startproblem.

Begränsningar av Azure diagnostics-tillägget inkluderar:

- Kan endast användas med Azure-resurser.
- Begränsad möjlighet att skicka data till Azure Monitor Logs.

## <a name="telegraf-agent"></a>Telegraf agent

[TillströmningData Telegraf-agenten](collect-custom-metrics-linux-telegraf.md) används för att samla in prestandadata från Linux-datorer till Azure Monitor Metrics.

Använd Telegraf agent om du behöver:

* Skicka data till [Azure Monitor Metrics](data-platform-metrics.md) för att analysera dem med [statistikutforskaren](metrics-getting-started.md) och för att dra nytta av funktioner som [mätaviseringar](../../azure-monitor/platform/alerts-metric-overview.md) i nära realtid och [automatisk skalning](autoscale-overview.md) (endast Linux). 



## <a name="dependency-agent"></a>Beroendeagent

Beroendeagenten samlar in identifierade data om processer som körs på den virtuella datorn och externa processberoenden. 

Använd beroendeagenten om du behöver:

* Använd kartfunktionen [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) eller [servicemappningslösningen.](../insights/service-map.md)

Tänk på följande när du använder beroendeagenten:

- Beroendeagenten kräver att Log Analytics-agenten installeras på samma virtuella dator.
- På virtuella Linux-datorer måste Log Analytics-agenten installeras före Azure Diagnostic Extension.

## <a name="extensions-compared-to-agents"></a>Tillägg jämfört med agenter

Log Analytics-tillägget för [Windows](../../virtual-machines/extensions/oms-windows.md) och [Linux](../../virtual-machines/extensions/oms-linux.md) installerar Log Analytics-agenten på virtuella Azure-datorer. Azure Monitor Dependency-tillägget för [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) och [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installerar beroendeagenten på virtuella Azure-datorer. Dessa är samma agenter som beskrivs ovan men kan du hantera dem genom [virtuella datortillägg](../../virtual-machines/extensions/overview.md). Du bör använda tillägg för att installera och hantera agenter när det är möjligt.


## <a name="next-steps"></a>Nästa steg

Få mer information om var och en av agenterna på följande:

- [Översikt över Log Analytics-agenten](log-analytics-agent.md)
- [Översikt över Azure Diagnostics-tillägg](diagnostics-extension-overview.md)
- [Samla in anpassade mått för en virtuell Linux-dator med Agenten InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
