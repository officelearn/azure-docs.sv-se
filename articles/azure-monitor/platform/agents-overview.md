---
title: Översikt över Azure Monitoring agents | Microsoft Docs
description: Den här artikeln innehåller en detaljerad översikt över tillgängliga Azure-agenter som stöder övervakning av virtuella datorer som finns i Azure eller hybrid miljö.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686672"
---
# <a name="overview-of-azure-monitor-agents"></a>Översikt över Azure Monitor agenter

Virtuella datorer och andra beräknings resurser kräver en agent för att samla in övervaknings data för att mäta prestanda och tillgänglighet för gäst operativ system och arbets belastningar. Den här artikeln beskriver de agenter som används av Azure Monitor och hjälper dig att avgöra vilka du behöver för att uppfylla kraven för din specifika miljö.

> [!NOTE]
> Azure Monitor har för närvarande flera agenter på grund av den senaste konsolideringen av Azure Monitor och Log Analytics. Även om det kan överlappa sina funktioner, har var och en har unika funktioner. Beroende på dina krav kan du behöva en eller flera agenter på dina virtuella datorer. 

Du kan ha en specifik uppsättning krav som inte kan uppfyllas helt med en enda agent för en viss virtuell dator. Du kanske till exempel vill använda mått aviseringar som kräver Azure Diagnostics-tillägg, men som även vill använda funktionerna i Azure Monitor for VMs som kräver Log Analytics-agenten och beroende agenten. I sådana fall kan du använda flera agenter och det här är ett vanligt scenario för kunder som behöver funktioner från var och en.

## <a name="summary-of-agents"></a>Sammanfattning av agenter

Följande tabeller ger en snabb jämförelse mellan Azure Monitors agenter för Windows och Linux. Ytterligare information finns i avsnittet nedan. 

### <a name="windows-agents"></a>Windows-agenter

| | Diagnostik<br>tillägg (WAD) | Log Analytics<br>Gent | Beroende<br>Gent |
|:---|:---|:---|:---|
| Miljöer som stöds | Azure | Azure<br>Annat moln<br>Lokalt | Azure<br>Annat moln<br>Lokalt | 
| Agent krav  | Inga | Inga | Kräver Log Analytics agent |
| Insamlade data | Händelseloggar<br>ETW-händelser<br>Prestanda<br>Filbaserade loggar<br>IIS-loggar<br>.NET-app-loggar<br>Kraschdumpar<br>Loggar för agent-diagnostik | Händelseloggar<br>Prestanda<IIS logs><br>Filbaserade loggar<br>Insikter och lösningar<br>Övriga tjänster | Process information och beroenden<br>Mått för nätverks anslutning |
| Data som skickas till | Azure Storage<br>Azure Monitor mått<br>Händelsehubb | Azure Monitor-loggar | Azure Monitor-loggar |


### <a name="linux-agents"></a>Linux-agenter

| | Diagnostik<br>tillägg (LAD) | Teleympkvistar<br>Gent | Log Analytics<br>Gent | Beroende<br>Gent |
|:---|:---|:---|:---|:---|
| Miljöer som stöds | Azure | Azure<br>Annat moln<br>Lokalt | Azure<br>Annat moln<br>Lokalt | Azure<br>Annat moln<br>Lokalt |
| Agent krav  | Inga | Inga | Inga | Kräver Log Analytics agent |
| Insamlade data | Syslog<br>Prestanda | Prestanda | Syslog<br>Prestanda| Process information och beroenden<br>Mått för nätverks anslutning |
| Data som skickas till | Azure Storage<br>Händelsehubb | Azure Monitor mått | Azure Monitor-loggar | Azure Monitor-loggar |

## <a name="log-analytics-agent"></a>Log Analytics-agent

[Log Analytics agent](log-analytics-agent.md) samlar in övervaknings data från gäst operativ systemet och arbets belastningar på virtuella datorer i Azure, andra moln leverantörer och lokalt. Data samlas in i en Log Analytics-arbetsyta. Log Analytics agenten är samma agent som används av System Center Operations Manager, och du kan använda datorer med fler Hem datorer för att kommunicera med hanterings gruppen och Azure Monitor samtidigt. Den här agenten krävs också av vissa insikter och lösningar i Azure Monitor.


> [!NOTE]
> Log Analytics agent för Windows kallas ofta Microsoft Monitoring Agent (MMA). Log Analytics agent för Linux kallas ofta OMS-agent.



Använd Log Analytics-agenten om du behöver:

* Samla in loggar och prestanda data från virtuella eller fysiska datorer utanför Azure. 
* Skicka data till en Log Analytics arbets yta för att dra nytta av funktioner som stöds av [Azure Monitor loggar](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) som [logg frågor](../log-query/log-query-overview.md).
* Använd [Azure Monitor for VMS](../insights/vminsights-overview.md) som gör att du kan övervaka dina virtuella datorer i skala och övervaka deras processer och beroenden på andra resurser och externa processer..  
* Hantera säkerheten för dina virtuella datorer med hjälp av [Azure Security Center](../../security-center/security-center-intro.md) eller [Azure Sentinel](../../sentinel/overview.md).
* Använd [Azure Automation uppdaterings hantering](../../automation/automation-update-management.md), [Azure Automation tillstånds konfiguration](../../automation/automation-dsc-overview.md)eller [Azure Automation ändringsspårning och inventering](../../automation/change-tracking.md) för att leverera omfattande hantering av dina virtuella Azure-datorer
* Använd olika [lösningar](../monitor-reference.md#insights-and-core-solutions) för att övervaka en viss tjänst eller ett visst program.

Begränsningar för Log Analytics agenten är:

- Det går inte att skicka data till Azure Monitor mått, Azure Storage eller Azure-Event Hubs.

## <a name="azure-diagnostics-extension"></a>Azure Diagnostics-tillägg

[Azure-diagnostik-tillägget](diagnostics-extension-overview.md) samlar in övervaknings data från gäst operativ systemet och arbets belastningar för virtuella Azure-datorer och andra beräknings resurser. Den samlar främst in data i Azure Storage, men låter dig också definiera data mottagare för att skicka data till andra destinationer, till exempel Azure Monitor mått och Azure-Event Hubs.

Använd Azure Diagnostic Extension om du behöver:

- Skicka data till Azure Storage för att arkivera eller analysera dem med verktyg som [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Skicka data till [Azure Monitor mått](data-platform-metrics.md) för att analysera den med [Mät Utforskaren](metrics-getting-started.md) och dra nytta av funktioner som nästan real tids [mått varningar](../../azure-monitor/platform/alerts-metric-overview.md) och [autoskalning](autoscale-overview.md) (endast Windows).
- Skicka data till tredje parts verktyg med [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Samla in [startdiagnostik](../../virtual-machines/troubleshooting/boot-diagnostics.md) för att undersöka start problem för virtuella datorer.

Begränsningar för Azure Diagnostics-tillägget är:

- Kan endast användas med Azure-resurser.
- Begränsad möjlighet att skicka data till Azure Monitor loggar.

## <a name="telegraf-agent"></a>Teleympkvistar-agent

[InfluxDatain-agenten](collect-custom-metrics-linux-telegraf.md) används för att samla in prestanda data från Linux-datorer för att Azure Monitor mått.

Använd teleympkvistar-agenten om du behöver:

* Skicka data till [Azure Monitor mått](data-platform-metrics.md) för att analysera den med [Mät Utforskaren](metrics-getting-started.md) och dra nytta av funktioner som nästan real tids [mått varningar](../../azure-monitor/platform/alerts-metric-overview.md) och [autoskalning](autoscale-overview.md) (endast Linux). 



## <a name="dependency-agent"></a>Beroendeagent

Beroende agenten samlar in identifierade data om processer som körs på den virtuella datorn och externa process beroenden. 

Använd beroende agenten om du behöver:

* Använd kart funktionen [Azure Monitor for VMS](../insights/vminsights-overview.md) eller [Tjänstkartas](../insights/service-map.md) lösningen.

Tänk på följande när du använder beroende agenten:

- Beroende agenten kräver att Log Analytics Agent installeras på samma virtuella dator.
- På virtuella Linux-datorer måste Log Analytics Agent installeras före Azure Diagnostic-tillägget.

## <a name="extensions-compared-to-agents"></a>Tillägg jämfört med agenter

Log Analytics-tillägget för [Windows](../../virtual-machines/extensions/oms-windows.md) och [Linux](../../virtual-machines/extensions/oms-linux.md) installerar Log Analytics-agenten på virtuella Azure-datorer. Azure Monitor beroende tillägg för [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) och [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installerar beroende agenten på virtuella Azure-datorer. Detta är samma agenter som beskrivs ovan, men du kan hantera dem via [tillägg för virtuella datorer](../../virtual-machines/extensions/overview.md). Du bör använda tillägg för att installera och hantera agenterna när det är möjligt.


## <a name="next-steps"></a>Nästa steg

Få mer information om var och en av agenterna på följande sätt:

- [Översikt över Log Analytics agent](log-analytics-agent.md)
- [Översikt över Azure-diagnostik-tillägg](diagnostics-extension-overview.md)
- [Samla in anpassade mått för en virtuell Linux-dator med InfluxData-agenten för teleympkvistar](collect-custom-metrics-linux-telegraf.md)
