---
title: Översikt över Azure-diagnostik-tillägget
description: Använd Azure Diagnostics för fel sökning, mäta prestanda, övervakning, trafik analys i moln tjänster, virtuella datorer och Service Fabric
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: 1bdefc6b61e4e5cc5b8648880c5fdd8662af1bc1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395358"
---
# <a name="what-is-azure-diagnostics-extension"></a>Vad är Azure-diagnostik tillägget
Azure-diagnostik tillägget är en agent i Azure som möjliggör insamling av diagnostikdata i ett distribuerat program. Du kan använda Diagnostics-tillägget från ett antal olika källor. För närvarande finns stöd för webb-och arbets roller i Azure Cloud Service (klassisk), Virtual Machines, skalnings uppsättningar för virtuella datorer och Service Fabric. Andra Azure-tjänster har olika diagnostiska metoder. Se [Översikt över övervakning i Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linux-agent
En [Linux-version av tillägget](../../virtual-machines/extensions/diagnostics-linux.md) är tillgänglig för Virtual Machines som kör Linux. Den insamlade statistiken och beteendet varierar från Windows-versionen.

## <a name="data-you-can-collect"></a>Data som du kan samla in
Azure-diagnostik-tillägget kan samla in följande typer av data:

| Datakälla | Beskrivning |
| --- | --- |
| Prestanda räknar mått |Operativ system och anpassade prestanda räknare |
| Program loggar |Spåra meddelanden som skrivits av ditt program |
| Windows-händelseloggar |Information som skickas till händelse loggnings systemet i Windows |
| .NET EventSource-loggar |Kod skrivnings händelser med hjälp av .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) -klassen |
| IIS-loggar |Information om IIS-webbplatser |
| [Manifestbaserade ETW-loggar](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |ETW (Event Tracing for Windows) händelser som genererats av en process. 81.1 |
| Krasch dum par (loggar) |Information om processens tillstånd om ett program kraschar |
| Anpassa felloggar |Loggar som skapats av ditt program eller din tjänst |
| Azure Diagnostic Infrastructure-loggar |Information om Azure-diagnostik |

(1) om du vill hämta en lista över ETW-providers kör `c:\Windows\System32\logman.exe query providers` i ett konsol fönster på den dator som du vill samla in information från.

## <a name="data-storage"></a>Datalagring
Tillägget lagrar data i ett [Azure Storage konto](diagnostics-extension-to-storage.md) som du anger.

Du kan också skicka den till [Application Insights](../../azure-monitor/app/cloudservices.md). 

Ett annat alternativ är att strömma det till [händelsehubben](../../event-hubs/event-hubs-about.md), som sedan gör att du kan skicka den till tjänster som inte är Azure-övervakning.

Du kan också välja att skicka data till Azure Monitor Metrics Time-Series-databas. För tillfället gäller den här sinken endast för prestanda räknare. Det gör att du kan skicka prestanda räknare i som anpassade mått. Den här funktionen är i för hands version. Azure Monitor-mottagaren stöder:
* Hämta alla prestanda räknare som skickats till Azure Monitor via [API: er för Azure Monitor mått.](https://docs.microsoft.com/rest/api/monitor/)
* Aviseringar om alla prestanda räknare som skickas till Azure Monitor via [mått varningar](../../azure-monitor/platform/alerts-overview.md) i Azure Monitor
* Behandlar operator med jokertecken i prestanda räknare som "instance"-dimensionen på måttet.  Om du till exempel har samlat in räknaren "logisk disk (\*)/DiskWrites/sec" kan du filtrera och dela på "instance"-dimensionen för att rita eller Varna vid disk skrivningar/s för varje logisk disk på den virtuella datorn (t. ex. C:)

Mer information om hur du konfigurerar den här sinken finns i [dokumentationen för Azure Diagnostics schema.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Kostnader
Vart och ett av alternativen ovan kan medföra kostnader. Var noga med att undersöka dem för att undvika oväntade räkningar.  Application Insights, Händelsehubben och Azure Storage har separata kostnader för inmatning och den tid som lagras. I synnerhet innehåller Azure Storage alla data för alltid, så du kanske vill rensa äldre data efter en viss tids period för att hålla kostnaderna nere.    

## <a name="versioning-and-configuration-schema"></a>Versions-och konfigurations schema
Se [Azure-diagnostik versions historik och schema](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Nästa steg
Välj den tjänst som du vill samla in diagnostik på och Använd följande artiklar för att komma igång. Använd de allmänna Azure Diagnostics-länkarna för att referera till vissa uppgifter.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services att använda Azure-diagnostik
* Om du använder Visual Studio läser [du använda Visual Studio för att spåra ett Cloud Services program](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) för att komma igång. Annars, se
* [Övervaka moln tjänster med Azure-diagnostik](../../cloud-services/cloud-services-how-to-monitor.md)
* [Konfigurera Azure-diagnostik i ett Cloud Services program](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Mer avancerade ämnen finns i

* [Använda Azure-diagnostik med Application Insights för Cloud Services](../../azure-monitor/app/cloudservices.md)
* [Spåra flödet av ett Cloud Services program med Azure-diagnostik](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Använd PowerShell för att konfigurera diagnostik på Cloud Services](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Om du använder Visual Studio läser [du använda Visual Studio för att spåra Azure-Virtual Machines](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) för att komma igång. Annars, se
* [Konfigurera Azure-diagnostik på en virtuell Azure-dator](/azure/virtual-machines/extensions/diagnostics-windows)

Mer avancerade ämnen finns i

* [Använd PowerShell för att konfigurera diagnostik på Azure Virtual Machines](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Skapa en virtuell Windows-dator med övervakning och diagnostik med Azure Resource Manager mall](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Kom igång med att [övervaka ett Service Fabric program](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Många andra Service Fabric-diagnostiska artiklar är tillgängliga i navigerings trädet till vänster när du kommer till den här artikeln.

## <a name="general-articles"></a>Allmänna artiklar
* Lär dig att [använda prestanda räknare i Azure-diagnostik](../../cloud-services/diagnostics-performance-counters.md).
* Om du har problem med att starta eller hitta dina data i Azure Storage-tabeller läser du [felsöka Azure-diagnostik](diagnostics-extension-troubleshooting.md)

