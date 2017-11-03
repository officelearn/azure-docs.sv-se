---
title: "Översikt över Azure Diagnostics | Microsoft Docs"
description: "Använda Azure-diagnostik för felsökning, mäta prestanda, övervakning, trafikanalyser i molntjänster, virtuella datorer och service fabric"
services: multiple
documentationcenter: .net
author: rboucher
manager: 
editor: 
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2017
ms.author: robb
ms.openlocfilehash: 0c6e4d9d2a3744f607b72364f3944c700acd070c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-diagnostics"></a>Vad är Azure-diagnostik
Azure-diagnostik är funktion i Azure som möjliggör insamling av diagnostikdata på ett distribuerat program. Du kan använda tillägget diagnostik från ett antal olika källor. Stöds för närvarande är Azure Cloud Service webb- och arbetsroller, Azure virtuella datorer som kör Microsoft Windows och Service Fabric. Andra Azure-tjänster har sina egna separata diagnostik.

## <a name="data-you-can-collect"></a>Du kan samla in data
Azure Diagnostics kan samla in följande typer av data:

| Datakälla | Beskrivning |
| --- | --- |
| Prestandaräknare |Operativsystem och anpassade prestandaräknare |
| Programloggar |Spåra meddelanden som skrivits av ditt program |
| Windows-händelseloggar |Informationen som skickas till Windows-händelse loggning system |
| Händelsekällan för .NET |Kod med hjälp av .NET-händelser skrivs [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) klass |
| IIS-loggar |Information om IIS-webbplatser |
| Manifestet baserat ETW |Event Tracing for Windows-händelser som genererats av någon annan process |
| Krasch minnesdumpar |Information om tillståndet hos processen om ett program kraschar |
| Anpassade fel |Loggarna som skapas i programmet eller tjänsten |
| Azure-diagnostik infrastrukturen loggar |Information om diagnostik själva |

Azure diagnostics-tillägget kan överföra data till ett Azure storage-konto eller skicka det till tjänster som [Programinsikter](../application-insights/app-insights-cloudservices.md). Du kan använda informationen för felsökning och felsökning, mäta prestanda, övervaka Resursanvändning, trafik analys och kapacitetsplanering och granskning.

## <a name="versioning"></a>Versionshantering
Se [Azure Diagnostics version historia](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Nästa steg
Välj vilken tjänst som du vill samla in diagnostik och Använd följande artiklar för att komma igång. Använd allmän Azure diagnostics länkarna för referens för specifika uppgifter.

## <a name="web-apps"></a>Web Apps
Observera att Web Apps inte använder Azure-diagnostik. Hitta motsvarande information på [Web Apps](../app-service/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Molntjänster med hjälp av Azure-diagnostik
* Om du använder Visual Studio, se [använda Visual Studio för att spåra ett molntjänster program](../vs-azure-tools-debug-cloud-services-virtual-machines.md) att komma igång. Annars går du till
* [Så här övervakar du molntjänster med hjälp av Azure-diagnostik](../cloud-services/cloud-services-how-to-monitor.md)
* [Konfigurera Azure-diagnostik i ett Cloud Services-tjänstprogram](../cloud-services/cloud-services-dotnet-diagnostics.md)

Mer avancerade ämnen, se

* [Med hjälp av Azure Diagnostics med Application Insights för molntjänster](../application-insights/app-insights-cloudservices.md)
* [Spåra flödet av ett Cloud Services-program med Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Använd PowerShell för att ställa in diagnostik på molntjänster](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines-using-azure-diagnostics"></a>Virtuella datorer med Azure-diagnostik
* Om du använder Visual Studio, se [använda Visual Studio för att spåra Azure Virtual Machines](../vs-azure-tools-debug-cloud-services-virtual-machines.md) att komma igång. Annars går du till
* [Konfigurera Azure-diagnostik på en virtuell dator i Azure](../virtual-machines-dotnet-diagnostics.md)

Mer avancerade ämnen, se

* [Använd PowerShell för att ställa in diagnostik på Azure Virtual Machines](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Skapa en Windows virtuell dator med övervakning och diagnostik med Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>Service Fabric med hjälp av Azure-diagnostik
Kom igång med [övervaka ett Service Fabric-program](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Många andra Service Fabric diagnostik artiklar är tillgängliga i navigeringsträdet till vänster när du kommer till den här artikeln.

## <a name="general-azure-diagnostics-articles"></a>Allmänna Azure Diagnostics artiklar
* [Azure Diagnostics Schema Configuration](https://msdn.microsoft.com/library/azure/mt634524.aspx) – Lär dig hur du ändrar schemafilen för att samla in och vidarebefordra diagnostikdata. Observera att du kan också använda Visual Studio för att ändra schemafilen.
* [Hur Azure-diagnostik data lagras i Azure Storage](../cloud-services/cloud-services-dotnet-diagnostics-storage.md) -känna till namnen på de tabeller och blobbar där diagnostiska data skrivs.
* Lär dig hur du [prestandaräknare i Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
* Lär dig hur du [väg Azure diagnostikinformation till Application Insights](azure-diagnostics-configure-application-insights.md)
* Om du har problem med att starta diagnostik eller söka efter data i Azure Storage-tabeller finns [felsöka Azure-diagnostik](azure-diagnostics-troubleshooting.md)
