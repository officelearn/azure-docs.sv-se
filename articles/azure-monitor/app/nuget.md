---
title: Azure Monitor Application Insights NuGet-paket
description: Azure Monitor Application Insights NuGet paketlistor för ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 5675c0fad61f1b75aa7e93db02246eed43967ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670006"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet paket

Nedan finns den aktuella listan över stabila release NuGet Paket för Application Insights.

## <a name="common-packages-for-aspnet"></a>Vanliga paket för ASP.NET

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInights Microsoft.ApplicationInsights Microsoft.ApplicationInsights Microsoft. | 2.12.0 | Tillhandahåller kärnfunktioner för överföring av alla Programinsikter Telemetrityper och är ett beroende paket för alla andra Application Insights-paket | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Avlyssna | 2.4.0 | Möjliggör avlyssning av metodanrop | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights Dependency Collector för .NET-program. Detta är ett beroende paket för Application Insights plattformsspecifika paket och ger automatisk samling av beroendetelemetri. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector Microsoft.ApplicationInsights.PerfCounterCollector Microsoft.ApplicationInsights.PerfCounterCollector Microsoft. | 2.12.0 | Application Insights Performance Counters Collector kan du skicka data som samlats in av prestandaräknare till Application Insights. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInights.Web | 2.12.0 | Application Insights för .NET-webbprogram | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Windows Server NuGet-paketet ger automatisk samling av programinsikter telemetri för .NET-program. Det här paketet kan användas som ett beroende paket för Application Insights plattformsspecifika paket eller som ett fristående paket för .NET-program som inte omfattas av plattformsspecifika paket (som för .NET-arbetsroller). | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Tillhandahåller en telemetrikanal till Application Insights Windows Server SDK som bevarar telemetri i offlinescenarier. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Vanliga paket för ASP.NET Core

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights för ASP.NET Core webbprogram. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInights Microsoft.ApplicationInsights Microsoft.ApplicationInsights Microsoft. | 2.12.0 | Det här paketet tillhandahåller kärnfunktioner för överföring av alla Application Insights Telemetrityper och är ett beroende paket för alla andra Application Insights-paket | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights Dependency Collector för .NET-program. Detta är ett beroende paket för Application Insights plattformsspecifika paket och ger automatisk samling av beroendetelemetri. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector Microsoft.ApplicationInsights.PerfCounterCollector Microsoft.ApplicationInsights.PerfCounterCollector Microsoft. | 2.12.0 | Application Insights Performance Counters Collector kan du skicka data som samlats in av prestandaräknare till Application Insights. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Windows Server NuGet-paketet ger automatisk samling av programinsikter telemetri för .NET-program. Det här paketet kan användas som ett beroende paket för Application Insights plattformsspecifika paket eller som ett fristående paket för .NET-program som inte omfattas av plattformsspecifika paket (som för .NET-arbetsroller). | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Tillhandahåller en telemetrikanal till Application Insights Windows Server SDK som bevarar telemetri i offlinescenarier. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Vanliga paket för Python med OpenCensus
| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Programinsikter för Python-program under Azure Monitor via OpenCensus. | [Ladda ner paket](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Det här paketet ger integration med Python [django-biblioteket.](https://pypi.org/project/django/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-kolv | 0.7.3 | Det här paketet ger integrering med [Python-kolvbiblioteket.](https://pypi.org/project/flask/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Det här paketet tillhandahåller integrering med Python [http.client-biblioteket](https://docs.python.org/3/library/http.client.html) för Python3 och [httplib](https://docs.python.org/2/library/httplib.html) för Python2. | [Ladda ner paket](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-loggning | 0.1.0 | Det här paketet berikar loggposter med spårningsdata. | [Ladda ner paket](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Det här paketet ger integrering med Python [mysql-connector-biblioteket.](https://pypi.org/project/mysql-connector/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | Det här paketet ger integrering med Python [psycopg2-biblioteket.](https://pypi.org/project/psycopg2/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Det här paketet ger integrering med Python [pymongo-biblioteket.](https://pypi.org/project/pymongo/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Det här paketet ger integrering med Python [PyMySQL-biblioteket.](https://pypi.org/project/PyMySQL/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-pyramid | 0.7.1 | Det här paketet ger integrering med [Python-pyramidbiblioteket.](https://pypi.org/project/pyramid/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-förfrågningar | 0.7.2 | Det här paketet ger integrering med [Python-begärandebiblioteket.](https://pypi.org/project/requests/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy opencensus-ext-sqlalchemy opencensus-ext-sqlalchemy openc | 0.1.2 | Det här paketet ger integrering med Python [SQLAlchemy-biblioteket.](https://pypi.org/project/SQLAlchemy/) | [Ladda ner paket](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Lyssnare/samlare/appenders

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Tillåter vidarebefordran av händelser från DiagnosticSource till Application Insights. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener gör det möjligt att skicka data från EventSource-händelser till Application Insights. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector Microsoft.ApplicationInsights.EtwCollector Microsoft.ApplicationInsights.EtwCollector Microsoft. | 2.7.2 | Application Insights EtwCollector gör det möjligt att skicka data från Event Tracing för Windows (ETW) till Application Insights. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | En anpassad TraceListener kan du skicka spårningsloggmeddelanden till Application Insights. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | En anpassad tilläggstjänst som gör att du kan skicka Log4Net-loggmeddelanden till Application Insights. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  ett anpassat mål som gör att du kan skicka NLog-loggmeddelanden till Application Insights. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Övervakar undantag i ditt program och samlar automatiskt in ögonblicksbilder för offlineanalys. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Det här paketet ger automatisk dekoration av telemetri med service fabric-kontexten som programmet körs i. Använd inte denna NuGet för Native Service Fabric-program. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Application Insights-modulen för tjänstinfrastrukturprogram. Använd denna NuGet endast för Native Service Fabric-program. Använd Microsoft.ApplicationInsights.ServiceFabric-paketet för program som körs i behållare. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Statusövervakare

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Aktiverar insamling av körningsdata för x64-program | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Aktiverar insamling av körningsdata för x86-program. | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Dessa paket utgör en del av kärnfunktionerna i körningsövervakningen i [Statusövervakaren](../../azure-monitor/app/monitor-performance-live-website-now.md). Du behöver inte ladda ner dessa paket direkt, bara använda Status Monitor installationsprogrammet. Om du vill förstå mer om hur dessa paket fungerar under huven detta [blogginlägg](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) från en av våra utvecklare är en bra början.

## <a name="additional-packages"></a>Ytterligare paket

| Paketnamn | Stabil version | Beskrivning | Ladda ned |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites Microsoft.ApplicationInsights.AzureWebSites Microsoft.ApplicationInsights.AzureWebSites Microsoft. | 2.6.5 | Det här tillägget aktiverar application insights-övervakningen på en Azure App Service. SDK version 2.6.1. Instruktioner: Lägg till "APPINSIGHTS_INSTRUMENTATIONKEY" programinställningar med din ikey och starta om webappen för att få effekt.| [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injektor | 2.6.7 | Det här paketet innehåller filer som krävs för kodlös Application Insights-injektion | [Ladda ner paket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Nästa steg

- Övervaka [ASP.NET Kärna](../../azure-monitor/app/asp-net-core.md).
- Profil ASP.NET Azure [Linux-webbappar](../../azure-monitor/app/profiler-aspnetcore-linux.md)för kärna .
- Felsöka ASP.NET [ögonblicksbilder](../../azure-monitor/app/snapshot-debugger.md).
