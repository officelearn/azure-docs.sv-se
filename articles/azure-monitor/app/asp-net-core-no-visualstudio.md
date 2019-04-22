---
title: Azure Application Insights för ASP.NET Core utan Visual Studio | Microsoft Docs
description: Övervaka ASP.NET Core-webbprogram för tillgänglighet, prestanda och användning.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288373"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights för ASP.NET Core-program

Den här artikeln beskriver steg för att aktivera Application Insights för en [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) program utan att använda Visual Studio IDE. Om du har Visual Studio IDE installeras sedan [detta](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) dokumentet innehåller anvisningar för Visual Studio. När du har slutfört instruktionerna i den här artikeln, startar Application Insights insamling av förfrågningar, beroenden, undantag, prestandaräknare, pulsslag och loggar från ASP.NET Core-program. Exempelprogrammet som används är en [MVC-appen](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) riktar in sig på `netcoreapp2.2`, men instruktionerna här kan användas för alla ASP.NET Core-program. Eventuella undantag framhävs när så är tillämpligt.

## <a name="supported-scenarios"></a>Scenarier som stöds

Application Insights SDK (Software Development Kit) för ASP.NET Core kan övervaka dina program oavsett var eller hur programmet körs. Om ditt program körs och har nätverksanslutning till Application Insights-tjänsten, förväntas telemetri samlas in. Detta stöd inkluderar, men är inte begränsat till alla operativsystem (Windows, Linux, Mac), värdmetoden (pågående vs out-of-process), distributionsmetod (framework-beroende eller fristående), webbserver (IIS, Kestrel), plattform (Azure Web Apps, Azure VM Docker, AKS och så vidare.) eller IDE (Visual Studio, VS Code kommandoraden).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En fungerande ASP.NET Core-program. Följ [detta](https://docs.microsoft.com/aspnet/core/getting-started/) guide för att skapa ett ASP.NET Core-program, om det behövs.
- En giltig Application Insights instrumentationsnyckel, vilket krävs för att skicka någon telemetri till Application Insights-tjänsten. Följ [dessa](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) anvisningar för att skapa en ny Application Insights-resurs, om det behövs och hämta en instrumenteringsnyckel.

## <a name="enabling-application-insights-server-side-telemetry"></a>Aktivera Application Insights från serversidan telemetri

1. Installera Application Insights SDK för ASP.NET Core, vilket är en vanlig NuGet-paketet. Vi rekommenderar att alltid använda den senaste stabila versionen. Några av de funktioner som beskrivs i den här artikeln kanske inte tillgänglig i tidigare versioner. Fullständig viktig information för SDK finns [här](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases). 

Följande visar ändringarna som ska läggas till .csproj projektfilen.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. Lägg till `services.AddApplicationInsightsTelemetry();` till `ConfigureServices()` -metod i din `Startup` klass. Fullständigt exempel nedan.

```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // The following line enables Application Insights telemetry collection.
        services.AddApplicationInsightsTelemetry();

        // code adding other services for your application
        services.AddMvc();
    }
```

3. Konfigurera instrumenteringsnyckeln.

   Även om det är möjligt att ange instrumenteringsnyckeln som ett argument till `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, rekommenderar vi att ange instrumenteringsnyckeln i konfigurationen. Följande visar hur du anger en instrumenteringsnyckel i `appsettings.json`. Kontrollera att `appsettings.json` har kopierats till rotmappen för programmet vid publicering.

```json
    {
      "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
      },
      "Logging": {
        "LogLevel": {
          "Default": "Warning"
        }
      }
    }
```

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` normalt används för att ange instrumenteringsnyckeln för program som distribueras till Azure Web Apps.

> [!NOTE]
> En instrumenteringsnyckel som anges i koden wins över miljövariabeln `APPINSIGHTS_INSTRUMENTATIONKEY`, vilket wins andra alternativ.

4. Kör ditt program och göra begäranden till den. Telemetri bör nu börjar flöda till Application Insights. Följande telemetri samlas in automatiskt av Application Insights SDK.

    1. Begäranden - inkommande webbegäranden till ditt program.
    1. Beroenden
        1. HTTP/Https-anrop som görs med `HttpClient`
        1. SQL-anrop som görs med `SqlClient`
        1. Azure storage-anrop som görs med [Azure Storage-klienten](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [Klient-SDK för EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) version 1.1.0 och senare
        1. [Klient-SDK för ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) version 3.0.0 och senare

             * Azure Cosmos DB spåras automatiskt endast om du använder HTTP/HTTPS. TCP-läge samlas inte in av Application Insights.


    1. [Prestandaräknare](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. Support för prestandaräknare i ASP.NET Core är begränsat till följande
            1. SDK-version 2.4.1 och samlar in prestandaräknare ovan om programmet körs i Azure Web App (Windows)
            1. SDK-version 2.7.0-beta3 och samlar in prestandaräknare ovan om programmet körs i Windows och riktar in sig på `NETSTANDARD2.0` eller högre.
            1. För program som fullständiga .NET Framework, stöds prestandaräknare i alla versioner av SDK.

            Det här dokumentet kommer att uppdateras när du lägger till stöd för räknaren av prestanda i Linux.
    1. [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` loggarna för allvarlighetsgrad `Warning` eller senare är automatiskt hämtade från SDK version 2.7.0-beta3 eller högre. Läs mer [här](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

Det kan ta några minuter för telemetri att starta visas i portalen. Att snabbt kontrollera om allt fungerar, är det bäst att använda [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), medan gör förfrågningar till programmet.

## <a name="send-ilogger-logs-to-application-insights"></a>Skicka loggar med ILogger till Application Insights

Application Insights stöder in loggar som skickas via ILogger. Läs den fullständiga dokumentationen [här](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="enable-client-side-telemetry-for-web-applications"></a>Aktivera telemetri för klientsidan för webbprogram

Stegen ovan är tillräckliga för att börja samla in telemetri på serversidan. Om ditt program har-komponenterna på klientsidan, följer du stegen nedan för att börja samla in [användningstelemetri](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) därifrån.

1. Lägg till inmatning i _ViewImports.cshtml:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. I _Layout.cshtml, infoga HtmlHelper i slutet av `<head>` avsnittet men före eventuella andra skript. Alla anpassade JavaScript-telemetri som du vill köra rapporten från sidan ska matas efter det här kodfragmentet:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Ändra filnamnen enligt själva programmet. De namn som används ovan är från en standardmall för MVC-program.

## <a name="configuring-application-insights-sdk"></a>Konfigurera Application Insights SDK

Application Insights SDK för ASP.NET Core kan anpassas för att ändra standardkonfigurationen. Användare av Application Insights ASP.NET SDK kanske är bekant med konfiguration med hjälp av `ApplicationInsights.config`, eller genom att ändra `TelemetryConfiguration.Active`. För ASP.NET Core görs konfiguration på olika sätt. ASP.NET Core SDK har lagts till i programmet med ASP.NET Core inbyggda [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) mekanism och konfigurera samma skulle också använda DependencyInjection. Nästan alla konfigurationsändringar görs den `ConfigureServices()` -metoden för din `Startup.cs` class, om inget annat anges. Följ i avsnitten nedan om du vill veta mer.

> [!NOTE]
> Det är viktigt att Observera att ändra konfigurationen genom att ändra `TelemetryConfiguration.Active` rekommenderas inte i ASP.NET Core-program.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Konfigurera med ApplicationInsightsServiceOptions

Det går att ändra några vanliga inställningar genom att skicka `ApplicationInsightsServiceOptions` till `services.AddApplicationInsightsTelemetry();`. Ett exempel visas nedan.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

Den exakta listan över konfigurerbara inställningar i `ApplicationInsightsServiceOptions` finns [här](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Samling

Application Insights SDK för ASP.NET Core stöder både FixedRate och Adaptiv sampling. Adaptiv sampling är aktiverat som standard. Följ [detta](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) dokumentet för att lära dig hur du konfigurerar sampling för ASP.NET Core-program.

### <a name="adding-telemetryinitializers"></a>Att lägga till TelemetryInitializers

Att lägga till en ny `TelemetryInitializer`, läggs till i behållaren DependencyInjection enligt nedan. `TelemetryInitializer`s läggs till DependencyInjection behållaren hämtas av SDK automatiskt.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Ta bort TelemetryInitializers

Att ta bort alla eller specifika TelemetryInitializers som finns som standard, Använd följande exempelkod **när** anropa `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>Att lägga till TelemetryProcessors

Anpassad telemetri processorer kan läggas till i `TelemetryConfiguration` med hjälp av metoden tillägget `AddApplicationInsightsTelemetryProcessor` på `IServiceCollection`. Använd följande exempel.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurera eller ta bort standard TelemetryModules

Följande Automatisk insamling moduler är aktiverade som standard och ansvarar för att automatiskt samla in telemetri. De kan inaktiveras och konfigureras om du vill ändra standardbeteendet.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

Konfigurera eventuella standard `TelemetryModule`, använda metoden tillägget `ConfigureTelemetryModule<T>` på `IServiceCollection` som visas i exemplet nedan.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurera telemetri Channel

Standardkanal som används är den `ServerTelemetryChannel`. Den kan åsidosättas med följande exempel nedan.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

*1. Jag vill spåra vissa ytterligare telemetri förutom telemetri som samlas in automatiskt. Hur gör jag det?*

* Skapa en instans av `TelemetryClient` av med hjälp av konstruktor inmatning och anropa de nödvändiga `TrackXXX()` metoden på den. Det rekommenderas inte att skapa en ny `TelemetryClient` instanser i ASP.NET Core-program som en singleton-instans av `TelemetryClient` är redan registrerat i den DI behållare som delar `TelemetryConfiguration` med resten av telemetrin. Skapa en ny `TelemetryClient` instans rekommenderas bara om den måste ha en separat konfigurationsfil från resten av telemetri. I följande exempel visas hur du spårar ytterligare telemetri från en domänkontrollant.

```csharp
public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Referera till [anpassade mått för Application Insights API-referens](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) beskrivning av anpassade data rapportering i Application Insights.

*2. En del Visual Studio-mallar används UseApplicationInsights() tilläggsmetod på IWebHostBuilder för att aktivera Application Insights. Den här användningen fortfarande är giltig?*

* Aktivera Application Insights med den här metoden är giltigt och används i Visual Studio registreringen och i Azure Web App-tillägg. Men det rekommenderas att använda `services.AddApplicationInsightsTelemery()` eftersom det ger dig överlagringar för att styra viss konfiguration. Både metoden gör internt samma sak, om ingen anpassad konfiguration som ska användas, anropande antingen är bra.

*3. Jag distribuera ASP.NET Core-program till Azure Web Apps. Bör jag fortfarande aktivera Application Insights-tillägget från Web Apps?*

* Om SDK är installerat under utvecklingen som visas i den här artikeln finns det inget behov av att aktivera Application Insights-tillägget från Web Apps-portalen. Även om tillägget installeras det kommer backoff, när det upptäcker att SDK har redan lagts till programmet. Aktivera Application Insights från tillägget behöver du inte installera och uppdatera SDK till ditt program. Aktivera Application Insights enligt den här artikeln är dock mer flexibel skäl nedan.
    1. Application Insights Telemetry fortsätter att fungera
        1. Alla operativsystem – Windows, Linux, Mac.
        1. Alla publicera läge – fristående eller Framework-beroende.
        1. Alla mål ramverk, inklusive fullständiga .NET Framework.
        1. Alla Hosting alternativ – Azure Web APP, virtuella datorer, Linux, behållare, AKS, icke-Azure.
    1. Telemetri kan ses lokalt, när du felsöker från Visual Studio.
    1. Gör att spåra ytterligare anpassad telemetri med `TrackXXX()` API.
    1. Har fullständig kontroll över konfigurationen.

*4. Kan jag aktivera Application Insights-övervakning med hjälp av verktyg som statusövervakaren?*

* Nej. [Statusövervakaren](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) och kommande har ersatts [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) stöder för närvarande endast ASP.NET. Dokumentet kommer att uppdateras när stöd för ASP.NET Core-program finns.

*5. Jag har ett ASP.NET Core 2.0-program? Inte Application Insights aktiveras automatiskt för dem utan mig gör ingenting?*

* `Microsoft.AspNetCore.All` 2.0 metapackage ingår Application Insights SDK (version 2.1.0), och om du kör programmet under Visual Studio-felsökaren Visual Studio gör det möjligt för application insights och visas telemetri som lokalt i IDE själva. Telemetri skickades inte till Application Insights-tjänsten, om inte en instrumenteringsnyckel anges uttryckligen. Vi rekommenderar att du följer anvisningarna i den här artikeln för att aktivera Application Insights, även för 2.0 appar.

*6. Jag kör mitt program i Linux. Alla funktioner stöds i Linux samt?*

* Ja. Funktioner som stöds för SDK är samma i alla plattformar, med följande undantag:
    1. Prestandaräknare stöds inte ännu i icke-Windows. Det här dokumentet kommer att uppdateras när du lägger till Linux-support.
    1. Även om `ServerTelemetryChannel` är aktiverad som standard, om programmet körs i icke-windows, kanalen skapar inte automatiskt en lokal mapp för att hålla telemetri tillfälligt om nätverksproblem. Den här begränsningen gör telemetri till att gå förlorade om det finns tillfälliga nätverket eller serverproblem. Lösningen för det här problemet är för att konfigurera en lokal mapp för kanalen, enligt nedan.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```
