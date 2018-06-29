---
title: Översikt över Azure övervakningsagenterna | Microsoft Docs
description: Den här artikeln innehåller en detaljerad översikt över Azure agenterna tillgänglig som stöd för övervakning av virtuella Azure-datorer.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088675"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Översikt över Azure-agenter att övervaka virtuella Azure-datorer
Microsoft Azure tillhandahåller flera olika sätt att samla in olika typer av data från virtuella datorer som finns i Azure eller andra molntjänstleverantörer som kör Microsoft Windows och Linux.  Den här artikeln hjälper dig att beskriva skillnader och funktioner som är tillgängliga med varje agent att avgöra vilken stöder servicehantering eller allmänna Övervakningskrav.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Jämförelse mellan Azure-diagnostik- och Log Analytics-agenten
Dag i Azure det, finns två typer av agenterna som kan övervakas av en Azure VM - tillägg för Azure-diagnostik och Log Analytics-Agent för Linux och Windows.  Dessa agenter är grunden, utformade för att samla in mått och loggar och vidarebefordra till en databas. Det är dock där deras likheter avslutas.  

Den [Azure Diagnostics tillägget](../monitoring-and-diagnostics/azure-diagnostics.md), som har angetts för Azure Cloud Services eftersom den blev allmänt tillgänglig i 2010, är en agent som ger dig enkla samling diagnostikdata från en Azure IaaS-resurs som en virtuell dator och Spara den till Azure-lagring.  När i lagring, du har valt att visa ett av flera tillgängliga verktyg som [Server Explorer i Visual Studio](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md) och [Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Du kan välja att samla in:

* En fördefinierad uppsättning prestandaräknare för operativsystemet och händelseloggar du kan ange vilka att samla in. 
* Alla begäranden och misslyckade begäranden på en IIS-webbserver
* .NET-app spårningsloggarna utdata
* Händelsespårning för Windows (ETW)-händelser 
* Samla in händelser från syslog  
* Kraschdumpar 

Data kan också vidarebefordras till [Programinsikter](../application-insights/app-insights-cloudservices.md), [logganalys](../log-analytics/log-analytics-overview.md), eller till Azure-tjänster med hjälp av [Händelsehubb](../event-hubs/event-hubs-what-is-event-hubs.md). 

För avancerad övervakning där du behöver mer än att samla in mått och en delmängd av loggar, krävs logganalys-agenten för Windows och Linux.  Med den här agenten ska du kunna använda Azure-tjänster, till exempel Automation och logganalys, inklusive en fullständig uppsättning funktioner, att leverera omfattande hantering av dina Azure virtuella datorer under deras livscykel. Det här omfattar:

* [Azure Automation-uppdateringshantering](../automation/automation-update-management.md) för uppdateringar av operativsystemet
* [Azure Automation önskat State Configuration](../automation/automation-dsc-overview.md) att upprätthålla konsekventa configuration tillstånd
* Spåra ändringar i konfiguration av med [ändringsspårning för Azure Automation och lager](../automation/automation-change-tracking.md)
* Samling av anpassade loggar från Operativsystemet och program med värdar som [FluentD](../log-analytics/log-analytics-data-sources-json.md), [anpassade loggar](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL och Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) med logganalys
* Azure-tjänster som [Programinsikter](https://docs.microsoft.com/azure/application-insights/) och [Azure Security Center](https://docs.microsoft.com/azure/security-center/) internt lagra sina data direkt i logganalys.  

## <a name="next-steps"></a>Nästa steg

- Se [samla in data från datorer i din miljö med logganalys](../log-analytics/log-analytics-concept-hybrid.md) att granska krav och tillgängliga metoder för att distribuera agenten till datorer i ditt datacenter eller andra molnmiljö.
- Om du vill konfigurera datainsamling från virtuella Azure-datorer läser du [Samla in data om Azure Virtual Machines](../log-analytics/log-analytics-quick-collect-azurevm.md). 