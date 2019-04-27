---
title: Azure Application Insights - beroende Automatisk insamling | Microsoft Docs
description: Application Insights automatiskt samla in och visualisera beroenden
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 3ad2f4788a765366066023724772f5432d0d56eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699213"
---
# <a name="application-insights-nuget-packages"></a>Application Insights-NuGet-paket

Nedan visas den aktuella listan över stabila versionen NuGet-paket för Application Insights.

## <a name="common-packages-for-aspnet"></a>Vanliga paket för ASP.NET

| Paketnamn | Säkra versionen | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Innehåller grundläggande funktioner för överföring av alla typer av Application Insights telemetri och ett beroende paket för alla andra Application Insights-paket | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Gör det möjligt för avlyssning av metodanrop | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Application Insights beroende insamlaren för .NET-program. Detta är ett beroende paket för Application Insights plattformsspecifika paket och tillhandahåller automatisk insamling av telemetri om beroenden. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights prestandaräknare insamlaren kan du skicka data som samlas in av prestandaräknare till Application Insights. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Application Insights för .NET-webbprogram | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet-paketet tillhandahåller automatisk insamling av telemetri för application insights för .NET-program. Det här paketet kan användas som ett beroende paket för Application Insights plattformsspecifika paket eller som ett fristående paket för .NET-program som inte omfattas av plattformsspecifika paket (som för .NET worker-roller). | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Tillhandahåller en kanal som telemetri till Application Insights Windows Server SDK som bevarar telemetri i offline-scenarier. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Vanliga paket för ASP.NET Core

| Paketnamn | Säkra versionen | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights för ASP.NET Core-webbprogram. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Det här paketet innehåller grundläggande funktioner för överföring av alla typer av Application Insights telemetri och ett beroende paket för alla andra Application Insights-paket | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Application Insights beroende insamlaren för .NET-program. Detta är ett beroende paket för Application Insights plattformsspecifika paket och tillhandahåller automatisk insamling av telemetri om beroenden. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights prestandaräknare insamlaren kan du skicka data som samlas in av prestandaräknare till Application Insights. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet-paketet tillhandahåller automatisk insamling av telemetri för application insights för .NET-program. Det här paketet kan användas som ett beroende paket för Application Insights plattformsspecifika paket eller som ett fristående paket för .NET-program som inte omfattas av plattformsspecifika paket (som för .NET worker-roller). | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Tillhandahåller en kanal som telemetri till Application Insights Windows Server SDK som bevarar telemetri i offline-scenarier. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Appenders-lyssnare /-insamlare

| Paketnamn | Säkra versionen | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Kan vidarebefordra händelser från DiagnosticSource till Application Insights. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener kan skicka data från EventSource händelser till Application Insights. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector kan skicka data från för Windows ETW (Event Tracing) till Application Insights. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | En anpassad TraceListener så att du kan skicka spårningsloggmeddelanden till Application Insights. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | En anpassad loggbilaga så att du kan skicka Log4Net loggmeddelanden till Application Insights. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  en anpassad mål så att du kan skicka NLog loggmeddelanden till Application Insights. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Övervakar undantagen i ditt program och samlar automatiskt in ögonblicksbilder för offlineanalys. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Paketnamn | Säkra versionen | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Det här paketet innehåller automatisk decoration av telemetri med service fabric-kontext programmet körs i. Använd inte den här NuGet för interna Service Fabric-program. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Application Insights-modulen för service fabric-program. Använd den här NuGet endast för interna Service Fabric-program. För program som körs i behållare, använder du Microsoft.ApplicationInsights.ServiceFabric package. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Statusövervakaren

| Paketnamn | Säkra versionen | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Aktiverar körning datainsamling för x64 program | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Aktiverar körning datainsamling för x86 program. | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Dessa paket som utgör en del av huvudfunktionerna i runtime övervakning i [statusövervakaren](../../azure-monitor/app/monitor-performance-live-website-now.md). Du behöver inte bara Använd installationsprogrammet för Status Monitor att hämta dessa paket direkt. Om du vill förstå mer om hur dessa paket arbeta under huven [blogginlägget](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) från någon av våra utvecklare är en bra början.

## <a name="additional-packages"></a>Ytterligare paket

| Paketnamn | Säkra versionen | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Det här tillägget kan Application Insights-övervakning i en Azure App Service. SDK-version 2.6.1. Instruktioner: Lägg till 'APPINSIGHTS_INSTRUMENTATIONKEY' programinställningar med din ikey och starta om webapp en gälla.| [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Det här paketet innehåller filer som krävs för kodlös Application Insights-inmatning | [Ladda ned paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Nästa steg

- Övervaka [ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Profilera ASP.NET Core [Azure Linux-webbappar](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Felsöka ASP.NET [ögonblicksbilder](../../azure-monitor/app/snapshot-debugger.md).