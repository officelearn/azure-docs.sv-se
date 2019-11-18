---
title: Översikt över Azure Monitoring agents | Microsoft Docs
description: Den här artikeln innehåller en detaljerad översikt över tillgängliga Azure-agenter som stöder övervakning av virtuella datorer som finns i Azure eller hybrid miljö.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: a01258799efa81c8d3ddba398facaa90c24c2513
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150038"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Översikt över Azure Monitors agenter 
Beräknings resurser, till exempel virtuella datorer, genererar data för att övervaka prestanda och tillgänglighet precis som [andra moln resurser](../insights/monitor-azure-resource.md). Beräknings resurser kan också ha ett gäst operativ system och arbets belastningar som måste övervakas. Att samla in dessa övervaknings data inifrån resursen kräver en agent. Den här artikeln beskriver de agenter som används av Azure Monitor och hjälper dig att avgöra vilka du behöver för att uppfylla kraven för din specifika miljö.

## <a name="summary-of-agents"></a>Sammanfattning av agenter

> [!NOTE]
> Azure Monitor har för närvarande flera agenter på grund av den senaste konsolideringen av Azure Monitor och Log Analytics. Båda agenterna delar vissa funktioner medan andra funktioner är unika för en viss agent. Beroende på dina krav kan du behöva en av agenterna eller både och. 

Azure Monitor har tre agenter som tillhandahåller vissa funktioner. Beroende på dina krav kan du installera en enskild agent eller flera på dina virtuella datorer och andra beräknings resurser.

* [Azure-diagnostik tillägg](#azure-diagnostic-extension)
* [Log Analytics agent](#log-analytics-agent)
* [Beroende agent](#dependency-agent)

Följande tabell ger en snabb jämförelse av de olika agenterna. Se resten av den här artikeln för information om var och en.

| | Azure Diagnostic-tillägg | Log Analytics agent | Beroendeagent |
|:---|:---|:---|:---|
| Miljöer som stöds | Azure | Azure<br>Annat moln<br>Lokal | Azure<br>Annat moln<br>Lokal |
| Operativsystem | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Agent beroenden  | Ingen | Ingen | Kräver Log Analytics agent |
| Data som samlas in | Händelseloggar<br>ETW-händelser<br>Syslog<br>Prestanda<br>IIS-loggar<br>Spårnings loggar för .NET-app<br>Kraschdumpar | Händelseloggar<br>Syslog<br>Prestanda<br>IIS-loggar<br>Anpassade loggar<br>Data från lösningar | Process information och beroenden<br>Mått för nätverks anslutning |
| Data som skickas till | Azure Storage<br>Azure Monitor mått<br>Händelsehubb | Azure Monitor-loggar | Azure Monitor-loggar |



## <a name="azure-diagnostic-extension"></a>Azure Diagnostic-tillägg
[Azure-diagnostik-tillägget](../../azure-monitor/platform/diagnostics-extension-overview.md) samlar in övervaknings data från gäst operativ systemet och arbets belastningar i Azure Compute-resurser. Den samlar främst in data i Azure Storage. Du kan konfigurera Azure Monitor att kopiera data från lagring till en Log Analytics arbets yta. Du kan också samla in prestanda data för gäst i Azure Monitor Mät värden.

Azure Diagnostic Extension kallas ofta WAD-(Windows Azure Diagnostic) eller LAD-tillägget (Linux Azure Diagnostic).


### <a name="scenarios-supported"></a>Scenarier som stöds

Scenarier som stöds av Azure-diagnostik-tillägget inkluderar följande:

* Samla in loggar och prestanda data från Azure Compute-resurser.
* Arkivera loggar och prestanda data från gäst operativ systemet till Azure Storage.
* Visa övervaknings data i lagrings utrymmen med hjälp av ett verktyg som [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
* Samla in prestanda data i en mått databas för att dra nytta av funktioner som stöds av [Azure Monitor mått](data-platform-metrics.md) , till exempel nära real tids [mått varningar](../../azure-monitor/platform/alerts-metric-overview.md) och [autoskalning](autoscale-overview.md). 
* Samla in övervaknings data från [lagring till en Log Analytics arbets yta](azure-storage-iis-table.md) för att dra nytta av funktioner som stöds av [Azure Monitor loggar](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) som [logg frågor](../log-query/log-query-overview.md).
* Skicka övervaknings data till tredje parts verktyg med [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
* Undersök start problem med den virtuella datorn med [startdiagnostik](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Kopiera data från program som körs i den virtuella datorn [till Application Insights](diagnostics-extension-to-application-insights.md) integrera med andra program övervakning.

## <a name="log-analytics-agent"></a>Log Analytics agent
[Log Analytics agent](log-analytics-agent.md) samlar in övervaknings data från gäst operativ systemet och arbets belastningar på virtuella datorer i Azure, andra moln leverantörer och lokalt. Data samlas in i en Log Analytics-arbetsyta.

Log Analytics agenten är samma agent som används av System Center Operations Manager, och du kan använda en grupp med fler Hem datorer för att kommunicera med hanterings gruppen och Azure Monitor samtidigt. Den här agenten krävs också av vissa lösningar i Azure Monitor.

Log Analytics agenten för Windows kallas ofta Microsoft Management Agent (MMA). Log Analytics agent för Linux kallas ofta OMS-agent.


### <a name="scenarios-supported"></a>Scenarier som stöds

Scenarier som stöds av Log Analytics-agenten innehåller följande:

* Samla in loggar och prestanda data från virtuella datorer i Azure, andra moln leverantörer och lokalt. 
* Samla in övervaknings data till en Log Analytics arbets yta för att dra nytta av funktioner som stöds av [Azure Monitor loggar](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) som [logg frågor](../log-query/log-query-overview.md).
* Använd Azure Monitor övervaknings lösningar som [Azure Monitor for VMS](../insights/vminsights-overview.md), [Azure Monitor för behållare](../insights/container-insights-overview.md)osv.  
* Hantera säkerheten för dina virtuella datorer med hjälp av [Azure Sentinel](../../sentinel/overview.md) som kräver Log Analytics-agenten.
* Hantera säkerheten för dina virtuella datorer med hjälp av [Azure Security Center](../../security-center/security-center-intro.md) som kräver Log Analytics-agenten.
* Använd funktionerna i [Azure Automation](../../automation/automation-intro.md) för att leverera omfattande hantering av dina virtuella Azure-datorer via deras livs cykel.  Exempel på de här funktionerna som kräver Log Analytics-agenten är:
  * [Azure Automation uppdaterings hantering](../../automation/automation-update-management.md) av operativ system uppdateringar.
  * [Azure Automation tillstånds konfiguration](../../automation/automation-dsc-overview.md) för att bibehålla ett konsekvent konfigurations tillstånd.
  * Spåra konfigurations ändringar med [Azure Automation ändringsspårning och inventering](../../automation/change-tracking.md).

## <a name="dependency-agent"></a>Beroendeagent
Beroende agenten samlar in identifierade data om processer som körs på den virtuella datorn och externa process beroenden. Den här agenten krävs för [tjänstkarta](../insights/service-map.md) och kart funktionen [Azure Monitor for VMS](../insights/vminsights-overview.md). Beroende agenten kräver Log Analytics agent och skriver data till en Log Analytics arbets yta i Azure Monitor.


## <a name="using-multiple-agents"></a>Använda flera agenter
Du kan ha särskilda krav för att använda antingen Azure Diagnostic Extension eller Log Analytics agent för en viss virtuell dator. Till exempel kanske du vill använda mått aviseringar som kräver Azure Diagnostic Extension. Men du kanske också vill använda kart funktionen i Azure Monitor for VMs som kräver beroende agenten och Log Analytics agenten. I det här fallet kan du använda flera agenter och det här är ett vanligt scenario för kunder som behöver funktioner från var och en.

### <a name="considerations"></a>Överväganden

- Beroende agenten kräver att Log Analytics Agent installeras på samma virtuella dator.
- På virtuella Linux-datorer måste Log Analytics Agent installeras före Azure Diagnostic-tillägget.


## <a name="next-steps"></a>Nästa steg

- Se [Översikt över den Log Analytics agenten](../../azure-monitor/platform/log-analytics-agent.md) för att granska krav och stödda metoder för att distribuera agenten till datorer som finns i Azure, i ditt data Center eller i annan moln miljö.

