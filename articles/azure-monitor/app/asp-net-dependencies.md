---
title: Beroendespårning i Azure Application Insights | Microsoft-dokument
description: Övervaka beroendeanrop från ditt lokala webbprogram eller Microsoft Azure-webbprogram med Application Insights.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1e30d8036c1fc624d39f027f38e314c6c57360f6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731498"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Beroendespårning i Azure Application Insights 

Ett *beroende* är en extern komponent som anropas av ditt program. Det är vanligtvis en tjänst som kallas med HTTP, en databas eller ett filsystem. [Application Insights](../../azure-monitor/app/app-insights-overview.md) mäter beroendeanropens varaktighet, oavsett om det inte fungerar eller inte, tillsammans med ytterligare information som namn på beroende och så vidare. Du kan undersöka specifika beroendeanrop och korrelera dem till begäranden och undantag.

## <a name="automatically-tracked-dependencies"></a>Spåras automatiskt beroenden

Application Insights SDK:er för .NET- och .NET Core-fartyg `DependencyTrackingTelemetryModule` som är en telemetrimodul som automatiskt samlar in beroenden. Den här beroendesamlingen aktiveras automatiskt för [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) och [ASP.NET Core-program,](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) när de konfigureras enligt de länkade officiella dokumenten. `DependencyTrackingTelemetryModule` levereras som [detta](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet-paket och förs automatiskt när du `Microsoft.ApplicationInsights.Web` använder `Microsoft.ApplicationInsights.AspNetCore`någon av NuGet-paketen eller .

 `DependencyTrackingTelemetryModule`spårar för närvarande följande beroenden automatiskt:

|Beroenden |Information|
|---------------|-------|
|Http/Https | Lokala eller Fjärr-http/https-samtal |
|WCF-anrop| Spåras endast automatiskt om Http-baserade bindningar används.|
|SQL | Samtal med `SqlClient`. Se [detta](#advanced-sql-tracking-to-get-full-sql-query) för att fånga SQL-frågan.  |
|[Azure-lagring (Blob, Tabell, Kö )](https://www.nuget.org/packages/WindowsAzure.Storage/) | Samtal som görs med Azure Storage Client. |
|[Sdk för EventHub-klient](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 och högre. |
|[ServiceBus-klient SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 och högre. |
|Azure Cosmos DB | Spåras endast automatiskt om HTTP/HTTPS används. TCP-läge kommer inte att fångas upp av Application Insights. |

Om du saknar ett beroende eller använder ett annat SDK, se till att det finns i listan över [automatiskt insamlade beroenden](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Om beroendet inte samlas in automatiskt kan du fortfarande spåra det manuellt med ett [spårberoendeanrop](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Konfigurera automatisk beroendespårning i Konsolappar

Om du automatiskt vill spåra beroenden från .NET-konsolappar installerar du Nuget-paketet `Microsoft.ApplicationInsights.DependencyCollector`och initierar `DependencyTrackingTelemetryModule` följande:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

För .NET Core-konsolappar är TelemetryConfiguration.Active föråldrad. Se vägledningen i [dokumentationen](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) till arbetartjänsten och [ASP.NET dokumentation om kärnövervakning](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

### <a name="how-automatic-dependency-monitoring-works"></a>Hur automatisk beroendeövervakning fungerar?

Beroenden samlas automatiskt in med hjälp av någon av följande tekniker:

* Använda bytekodsinstrumentering runt välj metoder. (InstrumentationEngine antingen från StatusMonitor eller Azure Web App Extension)
* Motringning av EventSource
* Callbacks för DiagnosticSource (i de senaste .NET/.NET Core SDK:erna)

## <a name="manually-tracking-dependencies"></a>Spåra beroenden manuellt

Följande är några exempel på beroenden, som inte samlas in automatiskt och därför kräver manuell spårning.

* Azure Cosmos DB spåras automatiskt endast om [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) används. TCP-läge kommer inte att fångas upp av Application Insights.
* Redis

För de beroenden som inte samlas in automatiskt av SDK kan du spåra dem manuellt med hjälp av [TrackDependency API](api-custom-events-metrics.md#trackdependency) som används av standard moduler för automatisk insamling.

Om du till exempel bygger din kod med en sammansättning som du inte har skrivit själv kan du tajt alla samtal till den för att ta reda på vilket bidrag den ger till dina svarstider. Om du vill att dessa data ska visas i beroendediagrammen i Application Insights skickar du dem med `TrackDependency`.

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

Alternativt `TelemetryClient` ger förlängningsmetoder `StartOperation` `StopOperation` och som kan användas för att manuellt spåra beroenden, som visas [här](custom-operations-tracking.md#outgoing-dependencies-tracking)

Om du vill stänga av standardmodulen för beroendespårning tar du bort referensen till DependencyTrackingTelemetryModule i [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) för ASP.NET program. För ASP.NET Core-program, följ instruktionerna [här](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Spåra AJAX-samtal från webbsidor

För webbsidor samlar Application Insights JavaScript SDK automatiskt ajax-anrop som beroenden.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Avancerad SQL-spårning för att få fullständig SQL Query

För SQL-anrop samlas alltid namnet på servern och databasen in `DependencyTelemetry`och lagras som namnet på de insamlade . Det finns ytterligare ett fält som kallas "data", som kan innehålla den fullständiga SQL-frågetexten.

För ASP.NET Core-program krävs inget ytterligare steg för att få den fullständiga SQL Query.

För ASP.NET-program samlas fullständig SQL-fråga in med hjälp av bytekodinstrumentering, vilket kräver instrumenteringsmotor. Ytterligare plattformsspecifika steg, som beskrivs nedan, krävs.

| Plattform | Steg som behövs för att få fullständig SQL Query |
| --- | --- |
| Azure Web App |Öppna [bladet Application Insights på kontrollpanelen](../../azure-monitor/app/azure-web-apps.md) för webbappen och aktivera SQL-kommandon under .NET |
| IIS Server (Azure VM, on-prem och så vidare.) | Använd Status Monitor PowerShell-modulen för att [installera instrumenteringsmotorn](../../azure-monitor/app/status-monitor-v2-api-reference.md) och starta om IIS. |
| Azure Cloud Service | Lägga till [startuppgift för att installera StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Din app ska vara inbyggd i ApplicationInsights SDK vid byggtid genom att installera NuGet-paket för [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) eller [ASP.NET Core-program](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Stöds inte

I ovanstående fall är det korrekta sättet att validera instrumenteringsmotorn korrekt installerat genom att `DependencyTelemetry` verifiera att SDK-versionen av insamlade är "rddp". 'rdddsd' eller 'rddf' anger att beroenden samlas in via DiagnosticSource eller EventSource callbacks, och därmed kommer fullständig SQL-fråga inte att fångas in.

## <a name="where-to-find-dependency-data"></a>Var du hittar beroendedata

* [Programöversikt](app-map.md) visualiserar beroenden mellan din app och närliggande komponenter.
* [Transaktionsdiagnostik](transaction-diagnostics.md) visar enhetliga, korrelerade serverdata.
* [Fliken Webbläsare](javascript.md) visar AJAX-samtal från användarnas webbläsare.
* Klicka dig vidare från långsamma eller misslyckade begäranden för att kontrollera deras beroendeanrop.
* [Analytics](#logs-analytics) kan användas för att fråga beroendedata.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a>Diagnostisera långsamma begäranden

Varje begäran händelse är associerad med beroende samtal, undantag och andra händelser som spåras medan din app bearbetar begäran. Så om vissa begäranden går dåligt kan du ta reda på om det beror på långsamma svar från ett beroende.

### <a name="tracing-from-requests-to-dependencies"></a>Spåra från begäranden till beroenden

Öppna fliken **Prestanda** och navigera till fliken **Beroenden** högst upp bredvid åtgärder.

Klicka på ett **beroendenamn** under övergripande. När du har valt ett beroende visas ett diagram över beroendefördelningen av varaktigheter till höger.

![På fliken Prestanda klickar du på fliken Beroende högst upp och sedan ett beroendenamn i diagrammet](./media/asp-net-dependencies/2-perf-dependencies.png)

Klicka på den blå **exempelknappen** längst ned till höger och sedan på ett exempel för att se transaktionsinformation från slutna till slutna dagar.

![Klicka på ett exempel för att se transaktionsinformation från slutna till slutna](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilera din livewebbplats

Ingen aning om vart tiden tar vägen? [Profilerar Application Insights](../../azure-monitor/app/profiler.md) spårar HTTP-anrop till din livewebbplats och visar funktionerna i koden som tog längst tid.

## <a name="failed-requests"></a>Misslyckade förfrågningar

Misslyckade begäranden kan också associeras med misslyckade anrop till beroenden.

Vi kan gå till fliken **Fel** till vänster och sedan klicka på **beroendefliken** högst upp.

![Klicka på diagrammet misslyckade begäranden](./media/asp-net-dependencies/4-fail.png)

Här kan du se antalet misslyckade beroenden. Om du vill ha mer information om en misslyckad förekomst som försöker klicka på ett beroendenamn i den nedre tabellen. Du kan klicka på den blå **beroendeknappen** längst ned till höger för att få transaktionsinformation från till.

## <a name="logs-analytics"></a>Loggar (Analys)

Du kan spåra beroenden i [Frågespråket Kusto](/azure/kusto/query/). Här följer några exempel.

* Hitta eventuella misslyckade beroendeanrop:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Hitta AJAX samtal:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Hitta beroendeanrop som är associerade med begäranden:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Hitta AJAX-samtal i samband med sidvisningar:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hur misslyckades automatisk anmälan om beroendeinsamlare till beroenden?*

* Misslyckade beroendeanrop kommer att ha fältet "framgång" inställt på Falskt. `DependencyTrackingTelemetryModule`rapporterar `ExceptionTelemetry`inte . Den fullständiga datamodellen för beroende beskrivs [här](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK med öppen källkod
Precis som alla Application Insights SDK är beroendesamlingsmodul också öppen källkod. Läs och bidra till koden eller rapportera problem på [den officiella GitHub-repoen](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Nästa steg

* [Undantag](../../azure-monitor/app/asp-net-exceptions.md)
* [Användardata & sidan](../../azure-monitor/app/javascript.md)
* [Tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
