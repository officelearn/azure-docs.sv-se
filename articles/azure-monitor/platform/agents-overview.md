---
title: Översikt över Azure övervakningsagenter | Microsoft Docs
description: Den här artikeln innehåller en detaljerad översikt över tillgängliga Azure agenterna som stöd för övervakning av virtuella datorer i Azure eller hybrid-miljö.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 89e73a4578134493a25ec3cbd2385433a2b36156
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775888"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Översikt över Azure övervakningsagenter 
Microsoft Azure tillhandahåller flera olika sätt att samla in olika typer av data från virtuella datorer som kör Microsoft Windows och Linux som ligger i Azure, ditt datacenter eller andra molnleverantörer. Det finns tre typer av agenter som kan övervaka en virtuell dator:

* Azure Diagnostics-tillägg
* Log Analytics-agenten för Linux och Windows
* Beroendeagent

Den här artikeln beskrivs skillnaderna mellan dem och deras funktioner för dig att avgöra vilken som stöder dina IT service management eller allmänt Övervakningskrav.  

## <a name="azure-diagnostic-extension"></a>Azure-diagnostiktillägget
Den [Azure-diagnostiktillägget](../../azure-monitor/platform/diagnostics-extension-overview.md) (vanligtvis kallad tillägget Windows Azure diagnostisk SÄKERHETSSPECIFIKA eller Linux Azure diagnostisk (LAD)) som har angetts för Azure Cloud Services eftersom det blev allmänt tillgänglig 2010. är en agent som ger enkel uppsättning diagnostikdata från en Azure-beräkningsresurs som en virtuell dator och spara dem till Azure storage. När i lagring som, du har valt att visa med någon av flera tillgängliga verktyg, till exempel [Server Explorer i Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) och [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Du kan välja att samla in:

* En fördefinierad uppsättning operativsystem prestandaräknare och händelseloggar, eller du kan ange som du vill samla in. 
* Alla begäranden och/eller misslyckade begäranden på en IIS-webbserver
* Spårning av utdataloggar .NET-app
* Händelsespårning för Windows-händelser (ETW) 
* Samla in händelser från syslog  
* Kraschdumpar 

Azure Diagnostics-agenten ska användas när du vill:

* Arkivera loggar och mått till Azure storage
* Integrera övervakningsdata med verktyg från tredje part. Dessa verktyg använder en mängd olika sätt, bland annat frågor till storage-konto, vidarebefordras till [Händelsehubbar](../../event-hubs/event-hubs-about.md), eller skicka en fråga med den [Azure Monitoring REST API](../../azure-monitor/platform/rest-api-walkthrough.md)
* Ladda upp data till Azure Monitor för att skapa diagram med mätvärden i Azure-portalen eller skapa nära realtid [måttaviseringar](../../azure-monitor/platform/alerts-metric-overview.md). 
* Skala VM scale sets och klassiska molntjänster baserat på OS gästmått.
* Undersöka startproblem med de Virtuella med [Startdiagnostik](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Förstå hur dina program fungerar och proaktivt identifierar problem påverkar dem med [Application Insights](../../azure-monitor/overview.md).
* Konfigurera Azure Monitor för att importera mått och loggar data som samlas in från molntjänster, klassiska virtuella datorer, och Service Fabric-noder som lagras i ett Azure storage-konto.

## <a name="log-analytics-agent"></a>Log Analytics-agenten
För avancerad övervakning där du behöver mer än att samla in mått och en delmängd av loggar, Log Analytics-agenten för Windows (kallas även som Microsoft Monitoring Agent (MMA)) och Linux måste anges. Log Analytics-agenten har utvecklats specifikt för omfattande hantering i den lokala fysiska och virtuella datorer, datorer som övervakas av System Center Operations Manager och virtuella datorer i finns i andra moln. Windows och Linux-agenter ansluter till en Log Analytics-arbetsyta i Azure Monitor för att samla in såväl lösningen som baseras övervakningsdata som anpassade datakällor som du konfigurerar.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Log Analytics-agenten ska användas när du vill:

* Samla in data från olika källor, både i Azure, andra molnleverantörer och lokala resurser. 
* Med någon av i Azure Monitor övervakningslösningar som [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md), [Azure Monitor för behållare](../insights/container-insights-overview.md)osv.  
* Använd en av de andra Azure-hanteringstjänster som [Azure Security Center](../../security-center/security-center-intro.md), [Azure Automation](../../automation/automation-intro.md)osv.

Tidigare flera Azure-tjänster har tillsammans i den *Operations Management Suite*, och därmed Log Analytics-agenten delas mellan tjänster som Azure Security Center och Azure Automation.  Detta inkluderar en fullständig uppsättning funktioner som de erbjuder leverera omfattande hantering av dina virtuella Azure-datorer under deras livscykel.  Några exempel på detta är:

* [Azure Automation uppdateringshantering](../../automation/automation-update-management.md) av uppdateringar av operativsystemet.
* [Azure Automation Desired State Configuration](../../automation/automation-dsc-overview.md) att upprätthålla konsekvent konfigurationsstatus.
* Spåra ändringar i konfigurationen med [Azure Automation-ändringsspårning och inventering](../../automation/automation-change-tracking.md).
* Azure-tjänster som [Programinsikter](https://docs.microsoft.com/azure/application-insights/) och [Azure Security Center](https://docs.microsoft.com/azure/security-center/), som internt lagrar sina data direkt i Log Analytics.  

## <a name="dependency-agent"></a>Beroendeagent
Beroendeagenten har utvecklats som en del av lösningen Tjänstkarta, som inte ursprungligen utvecklades av Microsoft. [Tjänstkarta](../insights/service-map.md) och [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) kräver en beroende-Agent på Windows och Linux virtuella datorer och den kan integreras med Log Analytics-agenten för att samla in identifierade data om processer som körs på den virtuella datorn och extern Processberoenden. Den lagrar dessa data i Log Analytics-arbetsytan och hjälper dig att visualisera de identifierade sammankopplade komponenterna.

Du kan behöva en kombination av dessa agenter att övervaka din virtuella dator. Agenter kan installeras sida vid sida som Azure-tillägg, men på Linux, Log Analytics-agenten *måste* installeras först. Annars installationen misslyckas. 

## <a name="next-steps"></a>Nästa steg

- Se [översikt över Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md) att granska krav och metoder som stöds för att distribuera agenten till datorerna i Azure, i ditt datacenter eller andra molnmiljö.

