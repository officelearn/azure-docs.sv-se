---
title: Programstatistik för Worker Service-appar (icke-HTTP-appar)
description: Övervakning av .NET Core/.NET Framework icke-HTTP-appar med Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f043140e5a342d114f777ad16bba588790b7f8cc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536734"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Programinsikter för Worker Service-program (icke-HTTP-program)

Application Insights släpper en ny SDK, kallad `Microsoft.ApplicationInsights.WorkerService`, som är bäst lämpad för icke-HTTP-arbetsbelastningar som meddelanden, bakgrundsuppgifter, konsolprogram etc. Dessa typer av program har inte begreppet en inkommande HTTP-begäran som en traditionell ASP.NET/ASP.NET Core Web Application, och därmed använda Application Insights paket för [ASP.NET](asp-net.md) eller [ASP.NET Core-program](asp-net-core.md) stöds inte.

Den nya SDK gör inte någon telemetrisamling av sig själv. Istället ger det i andra välkända Application Insights auto samlare som [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) etc. Den här SDK exponerar tilläggsmetoder för `IServiceCollection` att aktivera och konfigurera telemetrisamling.

## <a name="supported-scenarios"></a>Scenarier som stöds

[Application Insights SDK for Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) passar bäst för icke-HTTP-program oavsett var eller hur de körs. Om ditt program körs och har nätverksanslutning till Azure kan telemetri samlas in. Övervakning av application insights stöds överallt .NET Core stöds. Det här paketet kan användas i den nyligen introducerade [.NET Core 3.0 Worker Service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [bakgrundsuppgifter i Asp.Net Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), Konsolappar (.NET Core/ .NET Framework), etc.

## <a name="prerequisites"></a>Krav

En giltig instrumenteringsnyckel för Application Insights. Den här nyckeln krävs för att skicka telemetri till Application Insights. Om du behöver skapa en ny Application Insights-resurs för att hämta en instrumenteringsnyckel läser du [Skapa en application insights-resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Använda Application Insights SDK för arbetartjänster

1. Installera [paketet Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) i programmet.
   Följande kodavsnitt visar de ändringar som måste läggas till `.csproj` i projektets fil.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Anropa `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` förlängningsmetoden på `IServiceCollection`, vilket ger instrumenteringsnyckeln. Den här metoden bör anropas i början av programmet. Den exakta platsen beror på vilken typ av program.

1. Hämta `ILogger` en `TelemetryClient` instans eller instans från behållaren dependency injection (DI) genom att anropa `serviceProvider.GetRequiredService<TelemetryClient>();` eller använda konstruktorinjektion. Det här steget utlöser inställning av `TelemetryConfiguration` och automatisk insamlingsmoduler.

Specifika instruktioner för varje typ av applikation beskrivs i följande avsnitt.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0-arbetstjänstprogram

Fullständigt exempel delas [här](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Ladda ned och installera [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Skapa ett nytt Arbetartjänstprojekt antingen med hjälp av ny projektmall eller kommandorad i Visual Studio`dotnet new worker`
3. Installera [paketet Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) i programmet.

4. Lägg `services.AddApplicationInsightsTelemetryWorkerService();` till `CreateHostBuilder()` metoden `Program.cs` i klassen, som i det här exemplet:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Ändra `Worker.cs` ditt enligt nedanstående exempel.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Ställ in instrumenteringsnyckeln.

    Även om du kan ange instrumenteringsnyckeln som ett argument till `AddApplicationInsightsTelemetryWorkerService`rekommenderar vi att du anger instrumenteringsnyckeln i konfigurationen. Följande kodexempel visar hur du `appsettings.json`anger en instrumenteringsnyckel i . Kontrollera `appsettings.json` att den kopieras till programmets rotmapp under publiceringen.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Du kan också ange instrumenteringsnyckeln i någon av följande miljövariabler.
`APPINSIGHTS_INSTRUMENTATIONKEY` eller `ApplicationInsights:InstrumentationKey`

Exempel: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
Eller`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` Anger vanligtvis instrumenteringsnyckeln för program som distribueras till Web Apps som webbjobb.

> [!NOTE]
> En instrumenteringsnyckel som anges i `APPINSIGHTS_INSTRUMENTATIONKEY`kod vinner över miljövariabeln , som vinner över andra alternativ.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Grundläggande bakgrundsuppgifter med värdbaserade tjänster

[I](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) det här dokumentet beskrivs hur du skapar bakgrundsuppgifter i ASP.NET Core 2.1/2.2-programmet.

Fullständigt exempel delas [här](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Installera paketet Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) i programmet.
2. Lägg `services.AddApplicationInsightsTelemetryWorkerService();` till `ConfigureServices()` metoden, som i det här exemplet:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Följande är koden `TimedHostedService` för var bakgrundsaktivitetslogiken finns.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Ställ in instrumenteringsnyckeln.
   Använd samma `appsettings.json` sak från exemplet med .NET Core 3.0 Worker Service ovan.

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework Console-program

Som nämnts i början av den här artikeln kan det nya paketet användas för att aktivera Application Insights Telemetri från även ett vanligt konsolprogram. Det här [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)paketet är inriktat på och kan därför användas för konsolappar i .NET Core 2.0 eller senare och .NET Framework 4.7.2 eller senare.

Fullständigt exempel delas [här](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Installera paketet Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) i programmet.

2. Ändra Program.cs enligt nedanstående exempel.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Det här konsolprogrammet använder `TelemetryConfiguration`också samma standardprogram och kan anpassas på samma sätt som exemplen i det tidigare avsnittet.

## <a name="run-your-application"></a>Köra ditt program

Kör programmet. Exemplet arbetstagare från alla ovanstående gör en http-samtal varje sekund för att bing.com, och även avger några loggar med ILogger. Dessa rader är `StartOperation` insvept `TelemetryClient`i anropet av , som `RequestTelemetry` används för att skapa en åtgärd (i det här exemplet med namnet "operation"). Application Insights samlar in dessa ILogger-loggar (varning eller högre som standard) och `RequestTelemetry` beroenden, och de kommer att korreleras till relationen med överordnad och underordnad. Korrelationen fungerar också korsprocess/nätverksgräns. Om anropet till exempel gjordes till en annan övervakad komponent, kommer det att korreleras till den överordnade också.

Den här `RequestTelemetry` anpassade åtgärden kan ses som motsvarigheten till en inkommande webbbegäran i ett typiskt webbprogram. Även om det inte är nödvändigt att använda en operation, passar `RequestTelemetry` den bäst med [application insights-korrelationsdatamodellen](https://docs.microsoft.com/azure/azure-monitor/app/correlation) - med att fungera som den överordnade åtgärden och varje telemetri som genereras inuti arbetariterationen behandlas som logiskt tillhör samma åtgärd. Detta tillvägagångssätt säkerställer också att all telemetri som genereras `operation_id`(automatisk och manuell) kommer att ha samma . Eftersom sampling baseras `operation_id`på håller eller släpper samplingsalgoritmen antingen all telemetri från en enda iteration.

Följande listar den fullständiga telemetrin som automatiskt samlas in av Application Insights.

### <a name="live-metrics"></a>Live Metrics

[Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) kan användas för att snabbt kontrollera om Application Insights-övervakningen är korrekt konfigurerad. Även om det kan ta några minuter innan telemetri börjar visas i portalen och analytics, live mått skulle visa CPU-användning av den pågående processen i nära realtid. Det kan också visa andra telemetri som förfrågningar, beroenden, spår etc.

### <a name="ilogger-logs"></a>ILogger loggar

Loggar som avges `ILogger` via `Warning` allvarlighetsgrad eller större fångas in automatiskt. Följ [ILogger docs](ilogger.md#control-logging-level) för att anpassa vilka loggnivåer som fångas upp av Application Insights.

### <a name="dependencies"></a>Beroenden

Beroendesamling är aktiverad som standard. [I](asp-net-dependencies.md#automatically-tracked-dependencies) den här artikeln beskrivs de beroenden som samlas in automatiskt och innehåller även steg för manuell spårning.

### <a name="eventcounter"></a>EventCounter (EventCounter)

`EventCounterCollectionModule`är aktiverat som standard och samlar in en standarduppsättning räknare från .NET Core 3.0-appar. I självstudiekursen [EventCounter](eventcounters.md) visas standarduppsättningen med insamlade räknare. Den har också instruktioner om hur du anpassar listan.

### <a name="manually-tracking-additional-telemetry"></a>Spåra ytterligare telemetri manuellt

Medan SDK samlar automatiskt in telemetri enligt beskrivningen ovan, måste användaren i de flesta fall skicka ytterligare telemetri till Application Insights-tjänsten. Det rekommenderade sättet att spåra ytterligare telemetri `TelemetryClient` är genom att hämta en instans `TrackXXX()` av från Dependency Injection och sedan anropa en av de [API-metoder](api-custom-events-metrics.md) som stöds på den. Ett annat typiskt användningsfall är [anpassad spårning av åtgärder](custom-operations-tracking.md). Den här metoden visas i exemplen arbetar ovan.

## <a name="configure-the-application-insights-sdk"></a>Konfigurera SDK för programinsikter

Standardinställningen `TelemetryConfiguration` som används av arbetartjänsten SDK liknar den automatiska konfiguration som används i ett ASP.NET- eller ASP.NET Core-program, minus `HttpContext`telemetriinitializers som används för att berika telemetri från .

Du kan anpassa programstatistiken SDK för arbetartjänsten för att ändra standardkonfigurationen. Användare av Application Insights ASP.NET Core SDK kan vara bekanta med att ändra konfigurationen med hjälp av ASP.NET Core inbyggda [beroendeinjektion](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). WorkerService SDK baseras också på liknande principer. Gör nästan alla konfigurationsändringar i `ConfigureServices()` avsnittet `IServiceCollection`genom att anropa lämpliga metoder på , enligt beskrivningen nedan.

> [!NOTE]
> När du använder den här SDK-funktionen stöds inte ändring av konfiguration genom att `TelemetryConfiguration.Active` ändra, och ändringar kommer inte att återspeglas.

### <a name="using-applicationinsightsserviceoptions"></a>Använda ApplicationInsightsServiceOptions

Du kan ändra några vanliga `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`inställningar genom att gå till , som i det här exemplet:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Observera `ApplicationInsightsServiceOptions` att i den här SDK:n finns i namnområdet `Microsoft.ApplicationInsights.WorkerService` i motsats till `Microsoft.ApplicationInsights.AspNetCore.Extensions` i ASP.NET Core SDK.

Vanliga inställningar i`ApplicationInsightsServiceOptions`

|Inställning | Beskrivning | Default
|---------------|-------|-------
|AktiveraQuickPulseMetricStream | Aktivera/inaktivera livemetriska funktioner | true
|AktiveraAdaptiveSampling | Aktivera/inaktivera adaptiv sampling | true
|AktiveraHeartbeat | Aktivera/inaktivera pulsslagsfunktionen, som med jämna mellanrum (15 min standard) skickar ett anpassat mått med namnet "HeartBeatState" med information om körningen som .NET Version, Azure Environment-information, om tillämpligt, etc. | true
|AddAutoCollectedMetricExtractor | Aktivera/inaktivera autocollectedMetrics extractor, som är en TelemetriProcessor som skickar föraggregerade mått om begäranden/beroenden innan sampling sker. | true

Se de [konfigurerbara inställningarna i `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) för den senaste listan.

### <a name="sampling"></a>Samling

Application Insights SDK for Worker Service stöder både fast och adaptiv sampling. Adaptiv sampling är aktiverad som standard. Konfigurera sampling för Worker Service görs på samma sätt som för [ASP.NET Core Applications](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Lägga till telemetriinitializers

Använd [telemetriinitierare](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) när du vill definiera egenskaper som skickas med all telemetri.

Lägg till `TelemetryInitializer` nya `DependencyInjection` i behållaren och SDK `TelemetryConfiguration`lägger automatiskt till dem i .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Ta bort TelemetriInitializers

Telemetriinitierare finns som standard. Om du vill ta bort alla eller specifika telemetriinitierare använder du följande exempelkod *när* du har ringt `AddApplicationInsightsTelemetryWorkerService()`.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Lägga till telemetriprocessorer

Du kan lägga till anpassade `TelemetryConfiguration` telemetriprocessorer `AddApplicationInsightsTelemetryProcessor` `IServiceCollection`i tilläggsmetoden på . Du använder telemetriprocessorer i [avancerade filtreringsscenarier](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) för att möjliggöra mer direkt kontroll över vad som ingår eller utesluts från telemetrin som du skickar till application insights-tjänsten. Använd följande exempel.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurera eller ta bort standardmodulerar för telemetri

Application Insights använder telemetrimoduler för att automatiskt samla in telemetri om specifika arbetsbelastningar utan att kräva manuell spårning.

Följande moduler för automatisk insamling är aktiverade som standard. Dessa moduler ansvarar för att automatiskt samla in telemetri. Du kan inaktivera eller konfigurera dem så att de ändrar standardbeteendet.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Om du `TelemetryModule`vill konfigurera en `ConfigureTelemetryModule<T>` `IServiceCollection`standard använder du tilläggsmetoden på , som visas i följande exempel.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurera telemetrikanal

Standardkanalen är `ServerTelemetryChannel`. Du kan åsidosätta den som följande exempel visar.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Inaktivera telemetri dynamiskt

Om du vill inaktivera telemetri villkorligt och dynamiskt kan du lösa `TelemetryConfiguration` instansen med ASP.NET core `DisableTelemetry` dependency injection-behållaren var som helst i koden och ange flagga på den.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hur spårar jag telemetri som inte samlas in automatiskt?

Få en `TelemetryClient` instans av genom att använda `TrackXXX()` konstruktor injektion, och anropa den metod som krävs på den. Vi rekommenderar inte att `TelemetryClient` du skapar nya instanser. En singleton-instans av `TelemetryClient` är `DependencyInjection` redan registrerad `TelemetryConfiguration` i behållaren, som delar med resten av telemetrin. Skapa en `TelemetryClient` ny instans rekommenderas endast om den behöver en konfiguration som är skild från resten av telemetrin.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Kan jag använda Visual Studio IDE för att gå in application insights till ett Worker Service-projekt?

Introduktions- och in-spel i Visual Studio IDE stöds för närvarande endast för ASP.NET/ASP.NET Core-program. Det här dokumentet uppdateras när Visual Studio skickar stöd för arbetstjänstprogram som används ombord.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan jag aktivera application insights-övervakning med hjälp av verktyg som Statusövervakare?

Nej. [Statusövervakaren](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) och [statusövervakaren v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) stöder för närvarande endast ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Om jag kör mitt program i Linux, stöds alla funktioner?

Ja. Funktionsstöd för denna SDK är densamma på alla plattformar, med följande undantag:

* Prestandaräknare stöds endast i Windows med undantag för Process CPU/Minne som visas i Live Metrics.
* Även `ServerTelemetryChannel` om det är aktiverat som standard, om programmet körs i Linux eller MacOS, skapar kanalen inte automatiskt en lokal lagringsmapp för att hålla telemetri tillfälligt om det finns nätverksproblem. På grund av den här begränsningen går telemetri förlorade när det finns tillfälliga nätverk eller serverproblem. Du kan lösa problemet genom att konfigurera en lokal mapp för kanalen:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Exempelprogram

[.NET Core Console-program](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Använd det här exemplet om du använder ett konsolprogram skrivet i antingen .NET Core (2.0 eller senare) eller .NET Framework (4.7.2 eller senare)

[ASP .NET Core-bakgrundsuppgifter med HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Använd det här exemplet om du befinner dig i Asp.Net Core 2.1/2.2 och skapar bakgrundsuppgifter enligt officiell vägledning [här](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[.NET Core 3.0-arbetartjänst](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Använd det här exemplet om du har ett .NET Core 3.0 Worker Service-program enligt officiell vägledning [här](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK med öppen källkod

[Läs och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Nästa steg

* [Använd API:et](../../azure-monitor/app/api-custom-events-metrics.md) för att skicka egna händelser och mått för en detaljerad vy över appens prestanda och användning.
* [Spåra ytterligare beroenden som inte spåras automatiskt](../../azure-monitor/app/auto-collect-dependencies.md).
* [Berika eller filtrera automatisk insamlad telemetri](../../azure-monitor/app/api-filtering-sampling.md).
* [Beroendeinjektion i ASP.NET Kärna](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
