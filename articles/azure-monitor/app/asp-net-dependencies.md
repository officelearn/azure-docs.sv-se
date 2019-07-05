---
title: Beroendespårning i Azure Application Insights | Microsoft Docs
description: Övervaka beroendeanrop från din lokala eller Microsoft Azure-webbprogram med Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: d8ba5b19ad5d8f03203e9a028fbc5aec84e5ec06
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565376"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Beroendespårning i Azure Application Insights 

En *beroende* är en extern komponent som anropas av din app. Det är normalt en tjänst som kallas via HTTP, eller en databas eller ett filsystem. [Application Insights](../../azure-monitor/app/app-insights-overview.md) mäter varaktighet för beroendeanrop, om dess misslyckas eller inte, jämte ytterligare information som namn på beroende och så vidare. Du kan undersöka specifika beroendeanrop och jämföra dem med begäranden och undantag.

## <a name="automatically-tracked-dependencies"></a>Spåras automatiskt beroenden

Application Insights SDK: er för .NET och .NET Core levereras med `DependencyTrackingTelemetryModule` som är en telemetri-modul som samlar automatiskt in beroenden. Den här beroende samlingen aktiveras automatiskt för [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) och [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) program, när inställt efter länkade officiella dokumenten. `DependencyTrackingTelemetryModule` levereras som [detta](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet-paketet, och är online automatiskt när du använder något av NuGet-paketen `Microsoft.ApplicationInsights.Web` eller `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` för närvarande spårar automatiskt följande beroenden:

|Beroenden |Information|
|---------------|-------|
|Http/Https | Lokala eller fjärranslutna http/https-anrop |
|WCF-anrop| Endast spåras automatiskt om Http-baserade bindningar används.|
|SQL | Anrop som görs med `SqlClient`. Se [detta](##advanced-sql-tracking-to-get-full-sql-query) för att samla in SQL-fråga.  |
|[Azure storage (Blob, tabell, kö)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Anrop som görs med Azure Storage-klienten. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 och senare. |
|[ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 och senare. |
|Azure Cosmos DB | Endast spåras automatiskt om du använder HTTP/HTTPS. TCP-läge samlas inte in av Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Konfigurera automatisk beroendespårning i Konsolappar

Installera Nuget-paketet för att automatiskt spåra beroenden från.NET/.NET Core-konsolappar, `Microsoft.ApplicationInsights.DependencyCollector`, och initiera `DependencyTrackingTelemetryModule` på följande sätt:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Hur automatisk beroende övervakning fungerar?

Beroenden samlas automatiskt in på något av följande metoder:

* Med hjälp av byte kod instrumentation runt väljer metoder. (InstrumentationEngine från StatusMonitor eller Azure Web App Extension)
* EventSource återanrop
* DiagnosticSource återanrop (i den senaste.NET/.NET Core SDK: er)

## <a name="manually-tracking-dependencies"></a>Manuellt spåra beroenden

Här följer några exempel på beroenden som inte automatiskt insamlade och därför kräva manuell spårning.

* Azure Cosmos DB spåras automatiskt endast om [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) används. TCP-läge samlas inte in av Application Insights.
* Redis

För dessa beroenden inte automatiskt som samlas in av SDK, kan du spåra dem manuellt med hjälp av den [TrackDependency API](api-custom-events-metrics.md#trackdependency) som används av modulerna som standard automatiskt samling.

Om du bygger din kod med en sammansättning som du inte har skrivit själv kan du till exempel tid alla anrop till det, att ta reda på hur stora bidrag kommer görs till din svarstider. För att få dessa data visas i beroendediagrammen i Application Insights ska skicka den via `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Du kan också `TelemetryClient` ger tilläggsmetoder `StartOperation` och `StopOperation` som kan användas för att manuellt spåra beroenden, enligt [här](custom-operations-tracking.md#outgoing-dependencies-tracking)

Ta bort referensen till DependencyTrackingTelemetryModule i om du vill stänga av standard beroende-spårningsmodulen [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) för ASP.NET-program. Följ instruktionerna för ASP.NET Core-program, [här](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Spåra AJAX-anrop från webbsidor

För webbsidor, Application Insights JavaScript SDK samlar automatiskt in AJAX-anrop som beroenden som beskrivs [här](javascript.md#ajax-performance). Det här dokumentet fokuserar på beroenden från server-komponenter.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Avancerade SQL spårning för att få fullständig SQL-fråga

För SQL-anrop, namnet på servern och databasen alltid samlas in och lagras som namnet på den insamlade `DependencyTelemetry`. Det finns ytterligare ett fält som kallas ”data”, som kan innehålla den fullständiga texten för SQL-fråga.

Det finns inga ytterligare steg som krävs för att hämta den fullständiga SQL-fråga för ASP.NET Core-program.

För ASP.NET-program samlas fullständiga SQL-fråga med hjälp av byte kod instrumentation som kräver instrumentation motorn. Den ytterligare plattformsspecifika stegen är som beskrivs nedan, obligatoriska.

| Plattform | Steg som behövs för att få fullständig SQL-fråga |
| --- | --- |
| Azure-webbapp |I din Kontrollpanelen för webbappar, [öppnar du bladet Application Insights](../../azure-monitor/app/azure-web-apps.md) och aktivera SQL-kommandon under .NET |
| IIS-Server (virtuell Azure-dator, en lokal och så vidare.) | [Installera Status Monitor på servern där programmet körs](../../azure-monitor/app/monitor-performance-live-website-now.md) och starta om IIS.
| Azure Cloud Service | Lägg till [startåtgärd för att installera StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Din app ska vara integrerat ApplicationInsights SDK vid Byggtiden genom att installera NuGet-paket för [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) eller [ASP.NET Core-program](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Stöds inte

I sådana fall det korrekta sättet verifiera vilken instrumentation motorn är korrekt installerad är genom att verifiera att den SDK-versionen av insamlade `DependencyTelemetry` är 'rddp'. 'rdddsd' eller 'rddf' anger beroenden som samlas in via DiagnosticSource eller EventSource återanrop och kan därför fullständiga SQL-frågan inte kan avbildas.

## <a name="where-to-find-dependency-data"></a>Var du hittar data för programberoende

* [Programavbildning](app-map.md) hjälper dig att visualisera beroenden mellan din app och Närliggande komponenter.
* [Transaktionsdiagnostik](transaction-diagnostics.md) visar unified, korrelerade serverdata.
* [Fliken webbläsare](javascript.md#ajax-performance) visar AJAX-anrop från användarnas webbläsare.
* Klicka dig igenom från långsamma eller misslyckade begäranden om att kontrollera deras beroendeanrop.
* [Analytics](#logs-analytics) kan användas för att köra frågor mot beroendedata.

## <a name="diagnosis"></a> Diagnostisera långsamma begäranden

Varje begäran händelse är associerad med beroendeanrop, undantag och andra händelser som spåras medan appen är bearbetar begäran. Så om vissa begäranden gör felaktigt, kan du ta reda om det är på grund av långsam svar från ett beroende.

### <a name="tracing-from-requests-to-dependencies"></a>Spåra från begäranden till beroenden

Öppna den **prestanda** fliken och navigera till den **beroenden** fliken längst bredvid åtgärder.

Klicka på en **beroendenamn** under övergripande. När du har valt ett beroende som ett diagram över det beroendet fördelningen av varaktigheterna visas till höger.

![I prestanda fliken klickar du på fliken beroende överst sedan beroendenamn i diagrammet](./media/asp-net-dependencies/2-perf-dependencies.png)

Klicka på det blå fältet **exempel** knappen längst ner till höger och sedan på ett sampel kan se vilka transaktion slutpunkt till slutpunkt.

![Klicka på ett exempel kan se vilka transaktion slutpunkt till slutpunkt](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilera live-webbplatsen

Ingen aning där tiden går? Den [Application Insights-profileraren](../../azure-monitor/app/profiler.md) spårningar HTTP-anrop till din live-webbplatsen och visar funktionerna i din kod som tog den längsta tid.

## <a name="failed-requests"></a>Misslyckade förfrågningar

Misslyckade förfrågningar kan också vara kopplad till misslyckade anrop till beroenden.

Vi kan gå till den **fel** fliken till vänster och klicka sedan på den **beroenden** fliken högst upp.

![Klicka på diagram över misslyckade begäranden](./media/asp-net-dependencies/4-fail.png)

Här kommer du att kunna se antal misslyckade beroenden. Få mer information om en misslyckad förekomst försök att klicka på ett beroendenamn i den nedre tabellen. Du kan klicka på det blå fältet **beroenden** knappen längst ned till höger att hämta transaktionsinformation för slutpunkt till slutpunkt.

## <a name="logs-analytics"></a>Loggar (analys)

Du kan spåra beroenden i den [Kusto-frågespråket](/azure/kusto/query/). Här följer några exempel.

* Hitta alla misslyckade beroendeanrop:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Hitta AJAX-anrop:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Hitta beroendeanrop som associeras med förfrågningar:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Hitta AJAX-anrop som är associerade med sidvyer:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hur gör automatiska insamlaren beroenderapport misslyckades anrop till beroenden?*

* Misslyckade beroendeanrop har ”lyckades”-fältet inställd på False. `DependencyTrackingTelemetryModule` rapporterar inte `ExceptionTelemetry`. Fullständig datamodellen för beroende beskrivs [här](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Öppen källkod-SDK
Precis som alla Application Insights SDK är beroende datainsamlingsmodulen även öppen källkod. Läsa och bidra till koden eller rapportera problem vid [officiella GitHub-lagringsplatsen](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Nästa steg

* [Undantag](../../azure-monitor/app/asp-net-exceptions.md)
* [Användar-och siddata](../../azure-monitor/app/javascript.md)
* [Tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
