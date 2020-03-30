---
title: Azure Application Insights för ASP.NET Core-program | Microsoft-dokument
description: Övervaka ASP.NET Core-webbprogram för tillgänglighet, prestanda och användning.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: d6a0e507022452f1491e71651ba3bc8db3d1c090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284797"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights för ASP.NET Core-program

I den här artikeln beskrivs hur du aktiverar Application Insights för ett [ASP.NET Core-program.](https://docs.microsoft.com/aspnet/core) När du har slutfört instruktionerna i den här artikeln samlar Application Insights in begäranden, beroenden, undantag, prestandaräknare, pulsslag och loggar från ditt ASP.NET Core-program.

Exemplet vi kommer att använda här är ett `netcoreapp2.2` [MVC-program](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) som riktar sig till . Du kan använda dessa instruktioner på alla ASP.NET Core-program.

## <a name="supported-scenarios"></a>Scenarier som stöds

[Application Insights SDK för ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) kan övervaka dina program oavsett var eller hur de körs. Om ditt program körs och har nätverksanslutning till Azure kan telemetri samlas in. Övervakning av application insights stöds överallt .NET Core stöds. Support omfattar:
* **Operativsystem**: Windows, Linux eller Mac.
* **Hosting metod**: I processen eller ur processen.
* **Distributionsmetod**: Ramberoende eller fristående.
* **Webbserver**: IIS (Internet Information Server) eller Kestrel.
* **Värdplattform:** Funktionen Webbappar i Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS) och så vidare.
* **.NET Core Runtime version**: 1.XX, 2.XX eller 3.XX
* **IDE**: Visual Studio, VS-kod eller kommandorad.

> [!NOTE]
> Om du använder ASP.NET Core 3.X tillsammans med Application Insights använder du [2.8.0-versionen](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) eller senare. Detta är den enda versionen som stöder ASP.NET Core 3.X.

## <a name="prerequisites"></a>Krav

- Ett fungerande ASP.NET Core-program. Om du behöver skapa ett ASP.NET Core-program följer du den här [ASP.NET Core-självstudien](https://docs.microsoft.com/aspnet/core/getting-started/).
- En giltig instrumenteringsnyckel för Application Insights. Den här nyckeln krävs för att skicka telemetri till Application Insights. Om du behöver skapa en ny Application Insights-resurs för att hämta en instrumenteringsnyckel läser du [Skapa en application insights-resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Aktivera telemetri på application insights-servern (Visual Studio)

1. Öppna projektet i Visual Studio.

    > [!TIP]
    > Om du vill kan du ställa in källkontroll för projektet så att du kan spåra alla ändringar som Application Insights gör. Om du vill aktivera källkontroll väljer du **Filtillägg** > **i källkontroll**.

2. Välj **Projekt** > **lägg till programinsikter Telemetri**.

3. Välj **Kom igång**. Texten i det här valet kan variera beroende på vilken version av Visual Studio du har. Vissa tidigare versioner använder en **Start Free-knapp** i stället.

4. Välj din prenumeration. Välj sedan > **Resursregister**. **Resource**

5. När du har lagt till Application Insights i projektet kontrollerar du att du använder den senaste stabila versionen av SDK. Gå till **Project** > **Manage NuGet-paket** > **Microsoft.ApplicationInsights.AspNetCore**. Om du behöver välja **Uppdatera**.

     ![Skärmbild som visar var application insights-paketet ska väljas för uppdatering](./media/asp-net-core/update-nuget-package.png)

6. Om du har följt det valfria tipset och lagt till projektet i källkontrollen går du till **Visa** > **ändringar i Grupputforskaren** > **Changes**. Välj sedan varje fil för att se en diff-vy över de ändringar som gjorts av Application Insights telemetri.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Aktivera telemetri på application insights-servern (ingen Visual Studio)

1. Installera [Application Insights SDK NuGet-paketet för ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Vi rekommenderar att du alltid använder den senaste stabila versionen. Hitta fullständiga versionsanteckningar för SDK på [GitHub-repon för öppen källkod](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    I följande kodexempel visas de ändringar `.csproj` som ska läggas till i projektets fil.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Lägg `services.AddApplicationInsightsTelemetry();` till `ConfigureServices()` metoden `Startup` i klassen, som i det här exemplet:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Ställ in instrumenteringsnyckeln.

    Även om du kan ange instrumenteringsnyckeln som ett argument till `AddApplicationInsightsTelemetry`rekommenderar vi att du anger instrumenteringsnyckeln i konfigurationen. Följande kodexempel visar hur du `appsettings.json`anger en instrumenteringsnyckel i . Kontrollera `appsettings.json` att den kopieras till programmets rotmapp under publiceringen.

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

    Du kan också ange instrumenteringsnyckeln i någon av följande miljövariabler:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Ett exempel:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Anger `APPINSIGHTS_INSTRUMENTATIONKEY` vanligtvis instrumenteringsnyckeln för program som distribueras till Azure Web Apps.

    > [!NOTE]
    > En instrumenteringsnyckel som anges i `APPINSIGHTS_INSTRUMENTATIONKEY`kod vinner över miljövariabeln , som vinner över andra alternativ.

## <a name="run-your-application"></a>Köra ditt program

Kör programmet och gör förfrågningar till det. Telemetri bör nu flöda till Application Insights. Application Insights SDK samlar automatiskt in inkommande webbförfrågningar till ditt program, tillsammans med följande telemetri också.

### <a name="live-metrics"></a>Live Metrics

[Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) kan användas för att snabbt kontrollera om Application Insights-övervakningen är korrekt konfigurerad. Även om det kan ta några minuter innan telemetri börjar visas i portalen och analytics, live mått skulle visa CPU-användning av den pågående processen i nära realtid. Det kan också visa andra telemetri som förfrågningar, beroenden, spårningar, etc.

### <a name="ilogger-logs"></a>ILogger loggar

Loggar som avges `ILogger` via `Warning` allvarlighetsgrad eller större fångas in automatiskt. Följ [ILogger docs](ilogger.md#control-logging-level) för att anpassa vilka loggnivåer som fångas upp av Application Insights.

### <a name="dependencies"></a>Beroenden

Beroendesamling är aktiverad som standard. [I](asp-net-dependencies.md#automatically-tracked-dependencies) den här artikeln beskrivs de beroenden som samlas in automatiskt och innehåller även steg för manuell spårning.

### <a name="performance-counters"></a>Prestandaräknare

Stödet för [prestandaräknare](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) i ASP.NET Core är begränsat:

* SDK-versioner 2.4.1 och senare samlar in prestandaräknare om programmet körs i Azure Web Apps (Windows).
* SDK-versioner 2.7.1 och senare samlar in prestandaräknare om `NETSTANDARD2.0` programmet körs i Windows och mål eller senare.
* För program som är inriktade på .NET Framework stöder alla versioner av SDK prestandaräknare.
* SDK Versioner 2.8.0 och senare stöder cpu / minne räknare i Linux. Ingen annan räknare stöds i Linux. Det rekommenderade sättet att få systemräknare i Linux (och andra icke-Windows-miljöer) är att använda [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter (EventCounter)

`EventCounterCollectionModule`är aktiverat som standard och samlar in en standarduppsättning räknare från .NET Core 3.X-appar. I självstudiekursen [EventCounter](eventcounters.md) visas standarduppsättningen med insamlade räknare. Den har också instruktioner om hur du anpassar listan.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Aktivera telemetri på klientsidan för webbprogram

De föregående stegen är tillräckligt för att du ska kunna börja samla in telemetri på serversidan. Om programmet har komponenter på klientsidan följer du nästa steg för att börja samla in [användningstelemetri](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. I, `_ViewImports.cshtml`tillsätt injektion:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Infoga `_Layout.cshtml`i `HtmlHelper` slutet av `<head>` avsnittet men före något annat skript. Om du vill rapportera en anpassad JavaScript-telemetri från sidan injicerar du den efter det här kodavsnittet:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Alternativt att använda `FullScript` `ScriptBody` den är tillgänglig från och med SDK v2.14. Använd detta om du `<script>` behöver styra taggen för att ange en säkerhetsprincip för innehåll:

    ```cshtml
        <script> // apply custom changes to this script tag.
            @Html.Raw(JavaScriptSnippet.ScriptBody)
        </script>
    ```

De `.cshtml` filnamn som refererades tidigare kommer från en standardmall för MVC-program. Om du vill aktivera övervakning på klientsidan för ditt program måste JavaScript-kodavsnittet visas i `<head>` avsnittet på varje sida i programmet som du vill övervaka. Du kan uppnå det här målet för den här `_Layout.cshtml`programmallen genom att lägga till JavaScript-kodavsnittet i . 

Om projektet inte innehåller `_Layout.cshtml`kan du fortfarande lägga till [övervakning på klientsidan](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Du kan göra detta genom att lägga till JavaScript-kodavsnittet i en motsvarande fil som styr `<head>` alla sidor i appen. Eller så kan du lägga till kodavsnittet på flera sidor, men den här lösningen är svår att underhålla och vi rekommenderar det i allmänhet inte.

## <a name="configure-the-application-insights-sdk"></a>Konfigurera SDK för programinsikter

Du kan anpassa SDK för programinsikter för ASP.NET Core för att ändra standardkonfigurationen. Användare av Application Insights ASP.NET SDK kan vara bekanta `ApplicationInsights.config` med att `TelemetryConfiguration.Active`ändra konfigurationen med hjälp av eller genom att ändra . Du ändrar konfigurationen på olika sätt för ASP.NET Core. Lägg till ASP.NET Core SDK i programmet och konfigurera det med hjälp av ASP.NET Core inbyggd [beroendeinjektion](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Gör nästan alla konfigurationsändringar `Startup.cs` i `ConfigureServices()` klassens metod, om du inte dirigeras på annat sätt. Följande avsnitt innehåller mer information.

> [!NOTE]
> I ASP.NET Core-program stöds inte `TelemetryConfiguration.Active` ändring av konfiguration genom att ändra konfigurationen genom att ändra.

### <a name="using-applicationinsightsserviceoptions"></a>Använda ApplicationInsightsServiceOptions

Du kan ändra några vanliga `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`inställningar genom att gå till , som i det här exemplet:

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

Fullständig lista över inställningar i`ApplicationInsightsServiceOptions`

|Inställning | Beskrivning | Default
|---------------|-------|-------
|AktiveraPerformanceCounterCollectionModule  | Aktivera/inaktivera`PerformanceCounterCollectionModule` | true
|AktiveraRequestTrackingTelemetryModule   | Aktivera/inaktivera`RequestTrackingTelemetryModule` | true
|AktiveraEventCounterCollectionModule   | Aktivera/inaktivera`EventCounterCollectionModule` | true
|AktiveraBeroendeTrackingTelemetryModule   | Aktivera/inaktivera`DependencyTrackingTelemetryModule` | true
|AktiveraAppServicesHeartbeatTelemetryModule  |  Aktivera/inaktivera`AppServicesHeartbeatTelemetryModule` | true
|AktiveraAzureInstanceMetadataTelemetryModule   |  Aktivera/inaktivera`AzureInstanceMetadataTelemetryModule` | true
|AktiveraQuickPulseMetricStream | Aktivera/inaktivera livemetriska funktioner | true
|AktiveraAdaptiveSampling | Aktivera/inaktivera adaptiv sampling | true
|AktiveraHeartbeat | Aktivera/inaktivera pulsslagsfunktionen, som med jämna mellanrum (15 min standard) skickar ett anpassat mått med namnet "HeartBeatState" med information om körningen som .NET Version, Azure Environment-information, om tillämpligt, etc. | true
|AddAutoCollectedMetricExtractor | Aktivera/inaktivera autocollectedMetrics extractor, som är en TelemetriProcessor som skickar föraggregerade mått om begäranden/beroenden innan sampling sker. | true
|RequestCollectionOptions.TrackExceptions | Aktivera/inaktivera rapportering av ohanterad undantagsspårning av insamlingsmodulen För begäran. | false in NETSTANDARD2.0 (because Exceptions are tracked with ApplicationInsightsLoggerProvider), true otherwise.

Se de [konfigurerbara inställningarna i `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) för den senaste listan.

### <a name="sampling"></a>Samling

Application Insights SDK för ASP.NET Core stöder både fast hastighet och adaptiv sampling. Adaptiv sampling är aktiverad som standard. 

Mer information finns i [Konfigurera adaptiv sampling för ASP.NET Core-program](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Lägga till telemetriinitializers

Använd [telemetriinitierare](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) när du vill definiera globala egenskaper som skickas med all telemetri.

Lägg till `TelemetryInitializer` alla `DependencyInjection` nya i behållaren enligt följande kod. SDK hämtar automatiskt alla `TelemetryInitializer` som läggs till `DependencyInjection` i behållaren.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Ta bort TelemetriInitializers

Telemetriinitierare finns som standard. Om du vill ta bort alla eller specifika telemetriinitierare använder du följande exempelkod *när* du har ringt `AddApplicationInsightsTelemetry()`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

Du kan lägga till anpassade `TelemetryConfiguration` telemetriprocessorer `AddApplicationInsightsTelemetryProcessor` `IServiceCollection`i tilläggsmetoden på . Du använder telemetriprocessorer i [avancerade filtreringsscenarier](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer). Använd följande exempel.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurera eller ta bort standardmodulerar för telemetri

Application Insights använder telemetrimoduler för att automatiskt samla in användbar telemetri om specifika arbetsbelastningar utan att kräva manuell spårning av användaren.

Följande moduler för automatisk insamling är aktiverade som standard. Dessa moduler ansvarar för att automatiskt samla in telemetri. Du kan inaktivera eller konfigurera dem så att de ändrar standardbeteendet.

* `RequestTrackingTelemetryModule`- Samlar in RequestTelemetry från inkommande webbförfrågningar.
* `DependencyTrackingTelemetryModule`- Samlar DependencyTelemetry från utgående http-samtal och SQL-anrop.
* `PerformanceCollectorModule`- Samlar Windows PerformanceCounters.
* `QuickPulseTelemetryModule`- Samlar telemetri för att visas i Live Metrics portal.
* `AppServicesHeartbeatTelemetryModule`- Samlar in hjärtslag (som skickas som anpassade mått), om Azure App Service-miljö där programmet finns.
* `AzureInstanceMetadataTelemetryModule`- Samlar in hjärtslag (som skickas som anpassade mått), om Azure VM-miljö där programmet är värd.
* `EventCounterCollectionModule`- Samlar [EventCounters.](eventcounters.md) Denna modul är en ny funktion och finns i SDK Version 2.8.0 och högre.

Om du `TelemetryModule`vill konfigurera en `ConfigureTelemetryModule<T>` `IServiceCollection`standard använder du tilläggsmetoden på , som visas i följande exempel.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

Från och med 2.12.2 version, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) innehåller enkelt alternativ för att inaktivera någon av standardmodulerna.

### <a name="configuring-a-telemetry-channel"></a>Konfigurera en telemetrikanal

Standardkanalen är `ServerTelemetryChannel`. Du kan åsidosätta den som följande exempel visar.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Inaktivera telemetri dynamiskt

Om du vill inaktivera telemetri villkorligt och dynamiskt kan du lösa `TelemetryConfiguration` instansen med ASP.NET core `DisableTelemetry` dependency injection-behållaren var som helst i koden och ange flagga på den.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Ovanstående hindrar inte några automatiska insamlingsmoduler från att samla in telemetri. Endast sändning av telemetri till Application Insights inaktiveras med ovanstående metod. Om en viss automatisk insamlingsmodul inte önskas är det bäst att [ta bort telemetrimodulen](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-application-insights-support-aspnet-core-3x"></a>Stöder Application Insights ASP.NET Core 3.X?

Ja. Uppdatera till [Application Insights SDK för ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.8.0 eller senare. Äldre versioner av SDK stöder inte ASP.NET Core 3.X.

Om du använder Visual Studio-baserade instruktioner [härifrån](#enable-application-insights-server-side-telemetry-visual-studio)uppdaterar du också den senaste versionen av Visual Studio 2019 (16.3.0) till ombord. Tidigare versioner av Visual Studio stöder inte automatisk introduktion för ASP.NET Core 3.X-appar.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hur spårar jag telemetri som inte samlas in automatiskt?

Få en `TelemetryClient` instans av genom att använda `TrackXXX()` konstruktor injektion, och anropa den metod som krävs på den. Vi rekommenderar inte att `TelemetryClient` du skapar nya instanser i ett ASP.NET Core-program. En singleton-instans av `TelemetryClient` är `DependencyInjection` redan registrerad `TelemetryConfiguration` i behållaren, som delar med resten av telemetrin. Skapa en `TelemetryClient` ny instans rekommenderas endast om den behöver en konfiguration som är skild från resten av telemetrin.

I följande exempel visas hur du spårar ytterligare telemetri från en styrenhet.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Mer information om anpassad datarapportering i Application Insights finns i [Api-referens för Api för programinsikter](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Vissa Visual Studio-mallar använde metoden UseApplicationInsights() på IWebHostBuilder för att aktivera Application Insights. Är den här användningen fortfarande giltig?

Medan tilläggsmetoden `UseApplicationInsights()` fortfarande stöds markeras den föråldrad i Application Insights SDK version 2.8.0 och framåt. Det kommer att tas bort i nästa större version av SDK. Det rekommenderade sättet att aktivera Application Insights `AddApplicationInsightsTelemetry()` telemetri är med hjälp av eftersom det ger överbelastning för att styra vissa konfigurationer. I ASP.NET Core 3.X-appar `services.AddApplicationInsightsTelemetry()` är det enda sättet att aktivera programinsikter.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Jag distribuerar mitt ASP.NET Core-program till Web Apps. Ska jag fortfarande aktivera application insights-tillägget från Web Apps?

Om SDK installeras vid byggtid enligt den här artikeln behöver du inte aktivera [Application Insights-tillägget](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) från App Service-portalen. Även om tillägget är installerat, kommer det att backa när den upptäcker att SDK redan har lagts till i programmet. Om du aktiverar Application Insights från tillägget behöver du inte installera och uppdatera SDK. Men om du aktiverar Application Insights genom att följa instruktionerna i den här artikeln har du större flexibilitet eftersom:

   * Telemetrin Application Insights fortsätter att fungera i:
       * Alla operativsystem, inklusive Windows, Linux och Mac.
       * Alla publiceringslägen, inklusive fristående eller ramberoende.
       * Alla målramar, inklusive hela .NET Framework.
       * Alla värdalternativ, inklusive webbappar, virtuella datorer, Linux, behållare, Azure Kubernetes-tjänst och icke-Azure-värd.
       * Alla .NET Core-versioner inklusive förhandsgranskningsversioner.
   * Du kan se telemetri lokalt när du felsöker från Visual Studio.
   * Du kan spåra ytterligare anpassad telemetri med hjälp av API:et. `TrackXXX()`
   * Du har full kontroll över konfigurationen.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan jag aktivera application insights-övervakning med hjälp av verktyg som Statusövervakare?

Nej. [Statusövervakaren](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) och [statusövervakaren v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) stöder för närvarande endast ASP.NET 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Är Application Insights automatiskt aktiverat för mitt ASP.NET Core 2.0-program?

Den `Microsoft.AspNetCore.All` 2,0 metapackage ingår Application Insights SDK (version 2.1.0). Om du kör programmet under Felsökning av Visual Studio aktiverar Visual Studio Application Insights och visar telemetri lokalt i själva IDE:et. Telemetri skickades inte till application insights-tjänsten om inte en instrumenteringsnyckel angavs. Vi rekommenderar att du följer instruktionerna i den här artikeln för att aktivera Application Insights, även för 2.0-appar.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Om jag kör mitt program i Linux, stöds alla funktioner?

Ja. Funktionsstöd för SDK är detsamma på alla plattformar, med följande undantag:

* SDK samlar in [händelseräknare](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) på Linux eftersom [prestandaräknare](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) endast stöds i Windows. De flesta mått är desamma.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Stöds detta SDK för de nya .NET Core 3.X Worker Service-mallprogrammen?

Den här SDK kräver `HttpContext`och fungerar därför inte i alla icke-HTTP-program, inklusive .NET Core 3.X Worker Service-programmen. Se [det här](worker-service.md) dokumentet för att aktivera programinsikter i sådana program med hjälp av den nyutgivna Microsoft.ApplicationInsights.WorkerService SDK.

## <a name="open-source-sdk"></a>SDK med öppen källkod

[Läs och bidra till koden](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="video"></a>Video

- Kolla in den här externa steg-för-steg-videon för att [konfigurera Application Insights med .NET Core och Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) från grunden.
- Kolla in den här externa steg-för-steg-videon för att [konfigurera Application Insights med .NET Core och Visual Studio Code](https://youtu.be/ygGt84GDync) från grunden.

## <a name="next-steps"></a>Nästa steg

* [Utforska användarflöden](../../azure-monitor/app/usage-flows.md) för att förstå hur användarna navigerar i appen.
* [Konfigurera en ögonblicksbildsamling](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) för att se tillståndet för källkod och variabler när ett undantag genereras.
* [Använd API:et](../../azure-monitor/app/api-custom-events-metrics.md) för att skicka egna händelser och mått för en detaljerad vy över appens prestanda och användning.
* Använd [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) för att kontrollera din app hela tiden från hela världen.
* [Beroendeinjektion i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
