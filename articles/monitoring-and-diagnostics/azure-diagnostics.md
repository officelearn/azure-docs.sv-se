---
title: Översikt över Azure-diagnostik tillägget | Microsoft Docs
description: Använda Azure-diagnostik för felsökning, mäta prestanda, övervakning, trafikanalyser i molntjänster, virtuella datorer och service fabric
services: multiple
documentationcenter: .net
author: rboucher
manager: ''
editor: ''
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/01/2018
ms.author: robb
ms.openlocfilehash: daeaddefa461e71fcc62af4efc4fb7084b237cf9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="what-is-azure-diagnostics-extension"></a>Vad är Azure-diagnostik tillägget
Tillägget för Azure-diagnostik är en agent i Azure som möjliggör insamling av diagnostikdata på ett distribuerat program. Du kan använda tillägget diagnostik från ett antal olika källor. Stöds för närvarande är Azure Cloud Service (klassisk) webb- och arbetsroller, virtuella datorer, skaluppsättningar för den virtuella datorn och Service Fabric. Andra Azure-tjänster har olika diagnostik-metoder. Se [översikt över övervakning i Azure](monitoring-overview.md). 

## <a name="linux-agent"></a>Linux-Agent
En [Linux-versionen av tillägget](../virtual-machines/linux/diagnostic-extension.md) är tillgängligt för virtuella datorer som kör Linux. Statistik som samlas in och beteende variera från Windows-versionen. 

## <a name="data-you-can-collect"></a>Du kan samla in data
Azure-diagnostik-tillägget kan samla in följande typer av data:

| Datakälla | Beskrivning |
| --- | --- |
| Prestandaräknare |Operativsystem och anpassade prestandaräknare |
| Programloggar |Spåra meddelanden som skrivits av ditt program |
| Windows-händelseloggar |Informationen som skickas till Windows-händelse loggning system |
| Händelsekällan för .NET |Kod med hjälp av .NET-händelser skrivs [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) klass |
| IIS-loggar |Information om IIS-webbplatser |
| Manifestet baserat ETW |Event Tracing for Windows-händelser som genererats av någon annan process |
| Kraschdumpar |Information om tillståndet hos processen om ett program kraschar |
| Anpassa felloggar |Loggarna som skapas i programmet eller tjänsten |
| Azure-diagnostik infrastrukturen loggar |Information om diagnostik själva |

## <a name="data-storage"></a>Datalagring
Tillägget lagrar data i en [Azure Storage-konto](azure-diagnostics-storage.md) som du anger. 

Du kan också skicka den till [Programinsikter](../application-insights/app-insights-cloudservices.md). Ett annat alternativ är att överföra den till [Händelsehubb](../event-hubs/event-hubs-what-is-event-hubs.md), som sedan kan du skicka den till Azure-Övervakningscentral tjänster. 


## <a name="versioning-and-configuration-schema"></a>Schemat för versionshantering och konfiguration
Se [versionshistorik för Azure-diagnostik- och Schema](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>Nästa steg
Välj vilken tjänst som du vill samla in diagnostik och Använd följande artiklar för att komma igång. Använd allmän Azure diagnostics länkarna för referens för specifika uppgifter.

## <a name="cloud-services-using-azure-diagnostics"></a>Molntjänster med hjälp av Azure-diagnostik
* Om du använder Visual Studio, se [använda Visual Studio för att spåra ett molntjänster program](../vs-azure-tools-debug-cloud-services-virtual-machines.md) att komma igång. Annars går du till
* [Så här övervakar du molntjänster med hjälp av Azure-diagnostik](../cloud-services/cloud-services-how-to-monitor.md)
* [Konfigurera Azure-diagnostik i ett Cloud Services-tjänstprogram](../cloud-services/cloud-services-dotnet-diagnostics.md)

Mer avancerade ämnen, se

* [Med hjälp av Azure Diagnostics med Application Insights för molntjänster](../application-insights/app-insights-cloudservices.md)
* [Spåra flödet av ett Cloud Services-program med Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Använd PowerShell för att ställa in diagnostik på molntjänster](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuella datorer
* Om du använder Visual Studio, se [använda Visual Studio för att spåra Azure Virtual Machines](../vs-azure-tools-debug-cloud-services-virtual-machines.md) att komma igång. Annars går du till
* [Konfigurera Azure-diagnostik på en virtuell dator i Azure](../virtual-machines-dotnet-diagnostics.md)

Mer avancerade ämnen, se

* [Använd PowerShell för att ställa in diagnostik på Azure Virtual Machines](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Skapa en Windows virtuell dator med övervakning och diagnostik med Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Kom igång med [övervaka ett Service Fabric-program](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Många andra Service Fabric diagnostik artiklar är tillgängliga i navigeringsträdet till vänster när du kommer till den här artikeln.

## <a name="general-articles"></a>Allmänna artiklar
* Lär dig hur du [prestandaräknare i Azure-diagnostik](../cloud-services/diagnostics-performance-counters.md).
* Om du har problem med att starta diagnostik eller söka efter data i Azure storage-tabeller finns [felsöka Azure-diagnostik](azure-diagnostics-troubleshooting.md)
