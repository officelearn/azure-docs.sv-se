---
title: Azure Monitor Application Insights NuGet-paket
description: Azure Monitor Application Insights NuGet-paket listor för ASP.NET, ASP.NET Core, python
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/16/2018
ms.openlocfilehash: 0bac42242bc08fcc07bba12847a4aeb79424dc78
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406573"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet-paket

Nedan visas den aktuella listan över stabila release NuGet-paket för Application Insights.

## <a name="common-packages-for-aspnet"></a>Vanliga paket för ASP.NET

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Innehåller grundläggande funktioner för överföring av alla Application Insights Telemetry typer och är ett beroende paket för alla andra Application Insights-paket | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Aktiverar avlyssning av metod anrop | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights beroende insamlare för .NET-program. Detta är ett beroende paket för Application Insights plattformsspecifika paket och tillhandahåller automatisk insamling av beroende telemetri. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Med insamlaren Application Insights prestanda räknare kan du skicka data som samlas in av prestanda räknare till Application Insights. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | Application Insights för .NET-webb program | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Windows Server NuGet-paket tillhandahåller automatisk insamling av telemetri för Application Insights för .NET-program. Det här paketet kan användas som ett beroende paket för Application Insights plattformsspecifika paket eller som ett fristående paket för .NET-program som inte omfattas av plattformsspecifika paket (t. ex. för .NET Worker-roller). | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Tillhandahåller en telemetri-kanal för att Application Insights Windows Server SDK som ska bevara telemetri i offline-scenarier. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Vanliga paket för ASP.NET Core

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights för ASP.NET Core webb program. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Det här paketet innehåller grundläggande funktioner för överföring av alla Application Insights Telemetry typer och är ett beroende paket för alla andra Application Insights-paket | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights beroende insamlare för .NET-program. Detta är ett beroende paket för Application Insights plattformsspecifika paket och tillhandahåller automatisk insamling av beroende telemetri. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Med insamlaren Application Insights prestanda räknare kan du skicka data som samlas in av prestanda räknare till Application Insights. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Windows Server NuGet-paket tillhandahåller automatisk insamling av telemetri för Application Insights för .NET-program. Det här paketet kan användas som ett beroende paket för Application Insights plattformsspecifika paket eller som ett fristående paket för .NET-program som inte omfattas av plattformsspecifika paket (t. ex. för .NET Worker-roller). | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Tillhandahåller en telemetri-kanal för att Application Insights Windows Server SDK som ska bevara telemetri i offline-scenarier. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Vanliga paket för python med openräkning
| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| openinventering – ext – Azure | 1.0.0 | Application Insights för python-program under Azure Monitor via openräkning. | [Hämta paket](https://pypi.org/project/opencensus-ext-azure/) |
| openinventering – ext-Django | 0.7.2 | Det här paketet ger integrering med python [django](https://pypi.org/project/django/) -biblioteket. | [Hämta paket](https://pypi.org/project/opencensus-ext-django/) |
| openinventering – ext-kolv | 0.7.3 | Det här paketet ger integrering med python [kolv](https://pypi.org/project/flask/) -biblioteket. | [Hämta paket](https://pypi.org/project/opencensus-ext-flask/) |
| openinventering – ext-httplib | 0.7.2 | Det här paketet ger integrering med python [http. client-](https://docs.python.org/3/library/http.client.html) biblioteket för python3 och [httplib](https://docs.python.org/2/library/httplib.html) för Python2. | [Hämta paket](https://pypi.org/project/opencensus-ext-httplib/) |
| openinventering-ext-loggning | 0.1.0 | Det här paketet berikar logg poster med spårnings data. | [Hämta paket](https://pypi.org/project/opencensus-ext-logging/) |
| openinventering – ext-mysql | 0.1.2 | Det här paketet ger integrering med python [MySQL-Connector-](https://pypi.org/project/mysql-connector/) biblioteket. | [Hämta paket](https://pypi.org/project/opencensus-ext-mysql/) |
| openinventering – ext-postgresql | 0.1.2 | Det här paketet ger integrering med python [psycopg2](https://pypi.org/project/psycopg2/) -biblioteket. | [Hämta paket](https://pypi.org/project/opencensus-ext-postgresql/) |
| openinventering – ext-pymongo | 0.7.1 | Det här paketet ger integrering med python [pymongo](https://pypi.org/project/pymongo/) -biblioteket. | [Hämta paket](https://pypi.org/project/opencensus-ext-pymongo/) |
| openinventering – ext-pymysql | 0.1.2 | Det här paketet ger integrering med python [PyMySQL](https://pypi.org/project/PyMySQL/) -biblioteket. | [Hämta paket](https://pypi.org/project/opencensus-ext-pymysql/) |
| openinventering-ext-Pyramid | 0.7.1 | Det här paketet ger integrering med python [Pyramid](https://pypi.org/project/pyramid/) -biblioteket. | [Hämta paket](https://pypi.org/project/opencensus-ext-pyramid/) |
| openinventering – ext-begäranden | 0.7.2 | Det här paketet ger integrering med python- [begärandena](https://pypi.org/project/requests/) . | [Hämta paket](https://pypi.org/project/opencensus-ext-requests/) |
| openinventering – ext-sqlalchemy | 0.1.2 | Det här paketet ger integrering med python [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) -biblioteket. | [Hämta paket](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Lyssnare/insamlare/tillägg

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Tillåter vidarebefordran av händelser från DiagnosticSource till Application Insights. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener gör det möjligt att skicka data från EventSource-händelser till Application Insights. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector tillåter att data skickas från ETW (Event Tracing for Windows) (ETW) till Application Insights. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | En anpassad TraceListener som gör att du kan skicka spårnings logg meddelanden till Application Insights. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | En anpassad tillägg som gör att du kan skicka Log4Net-loggfiler till Application Insights. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  ett anpassat mål som gör att du kan skicka NLog-loggfiler till Application Insights. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Övervakar undantag i ditt program och samlar automatiskt in ögonblicks bilder för offline-analys. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Det här paketet innehåller automatisk dekoration av telemetri med den Service Fabric-kontext som programmet körs i. Använd inte den här NuGet för interna Service Fabric-program. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Application Insights modul för Service Fabric-program. Använd endast den här NuGet för interna Service Fabric-program. Använd Microsoft. ApplicationInsights. ServiceFabric-paketet för program som körs i behållare. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Status övervakare

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Aktiverar körnings data insamling för x64-program | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Aktiverar data insamling för körning av x86-program. | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Dessa paket utgör en del av kärn funktionerna i körnings övervakningen i [statusövervakare](../../azure-monitor/app/monitor-performance-live-website-now.md). Du behöver inte hämta paketen direkt, Använd bara installations programmet för Statusövervakare. Om du vill veta mer om hur dessa paket fungerar på det här sättet är [blogg inlägget](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) från någon av våra utvecklare en bra start.

## <a name="additional-packages"></a>Ytterligare paket

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | påverkar | Med det här tillägget kan Application Insights övervakas på en Azure App Service. SDK-version 2.6.1. Anvisningar: Lägg till program inställningar för APPINSIGHTS_INSTRUMENTATIONKEY med din iKey och starta om webapp för att få en verkan.| [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Det här paketet innehåller filer som krävs för att koda Application Insights-injektering | [Hämta paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Nästa steg

- Övervaka [ASP.net Core](../../azure-monitor/app/asp-net-core.md).
- Profil ASP.NET Core [Azure Linux-webbappar](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Felsök ASP.NET- [ögonblicksbilder](../../azure-monitor/app/snapshot-debugger.md).
