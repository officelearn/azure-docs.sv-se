---
title: Översikt över Azure övervakningsagenter | Microsoft Docs
description: Den här artikeln innehåller en detaljerad översikt över Azure-agenter tillgängliga som stöd för övervakning av virtuella Azure-datorer.
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
ms.openlocfilehash: 81db6720422de111cc5b390c58e9020d7c19f90a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282041"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Översikt över Azure-agenter att övervaka Azure-datorer
Microsoft Azure tillhandahåller flera olika sätt att samla in olika typer av data från virtuella datorer som finns i Azure eller andra molnleverantörer som kör Microsoft Windows och Linux.  Den här artikeln beskriver hur beskriver skillnaderna och funktioner som är tillgängliga med varje agent för dig att avgöra vilken som har stöd för din service management eller allmänna Övervakningskrav.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Jämföra Azure Diagnostics och Log Analytics-agenten
I dag i Azure finns det två typer av agenter som kan övervaka en Azure-dator – Azure Diagnostics-tillägg och Log Analytics-agenten för Linux och Windows.  De här agenterna är grunden, utformade för att samla in mått och loggar och vidarebefordra till en databas. Det är dock där deras likheter avslutas.  

Den [Azure-diagnostiktillägget](../monitoring-and-diagnostics/azure-diagnostics.md), som har angetts för Azure Cloud Services eftersom det blev allmänt tillgänglig i 2010, är en agent som ger enkel uppsättning diagnostikdata från en Azure IaaS-resurs som en virtuell dator, och och spara dem till Azure storage.  När i lagring som, du har valt att visa med någon av flera tillgängliga verktyg, till exempel [Server Explorer i Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) och [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Du kan välja att samla in:

* En fördefinierad uppsättning operativsystem prestandaräknare och händelseloggar, eller du kan ange som du vill samla in. 
* Alla begäranden och/eller misslyckade begäranden på en IIS-webbserver
* Spårning av utdataloggar .NET-app
* Händelsespårning för Windows (ETW)-händelser 
* Samla in händelser från syslog  
* Kraschdumpar 

Data kan också vidarebefordras till [Programinsikter](../application-insights/app-insights-cloudservices.md), [Log Analytics](../log-analytics/log-analytics-queries.md), eller att icke-Azure-tjänster med hjälp av [Event Hub](../event-hubs/event-hubs-about.md). 

För avancerad övervakning där du behöver mer än samla in mått och en delmängd av loggar, krävs Log Analytics-agenten för Windows och Linux.  Med den här agenten kan du använda Azure-tjänster, till exempel Automation och Log Analytics, inklusive den fullständiga uppsättningen funktioner som de erbjuder att leverera omfattande hantering av dina virtuella Azure-datorer under deras livscykel. Det här omfattar:

* [Azure Automation uppdateringshantering](../automation/automation-update-management.md) av uppdateringar av operativsystemet
* [Azure Automation Desired State Configuration](../automation/automation-dsc-overview.md) att upprätthålla konsekvent Konfigurationsstatus
* Spåra ändringar i konfigurationen med [Azure Automation-ändringsspårning och inventering](../automation/automation-change-tracking.md)
* Anpassade loggsamlingar från operativsystem och program som värdar [FluentD](../log-analytics/log-analytics-data-sources-json.md), [anpassade loggar](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL och Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) med Log Analytics
* Azure-tjänster som [Programinsikter](https://docs.microsoft.com/azure/application-insights/) och [Azure Security Center](https://docs.microsoft.com/azure/security-center/) internt lagrar sina data direkt i Log Analytics.  

## <a name="next-steps"></a>Nästa steg

- Se [samla in data från datorer i din miljö med Log Analytics](../log-analytics/log-analytics-concept-hybrid.md) att granska krav och tillgängliga metoder för att distribuera agenten till datorer i ditt datacenter eller andra moln.
- Om du vill konfigurera datainsamling från virtuella Azure-datorer läser du [Samla in data om Azure Virtual Machines](../log-analytics/log-analytics-quick-collect-azurevm.md). 
