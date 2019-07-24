---
title: Azure Application insikter om ASP.NET Core program | Microsoft Docs
description: Övervaka ASP.NET Core webb program för tillgänglighet, prestanda och användning.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: 7e0143a25c0bb25b936d072cc2652e8b38a0be66
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302702"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights för ASP.NET Core program

I den här artikeln beskrivs hur du aktiverar Application Insights för ett [ASP.net Core](https://docs.microsoft.com/aspnet/core) program. När du har slutfört instruktionerna i den här artikeln får Application Insights Samla in begär Anden, beroenden, undantag, prestanda räknare, pulsslag och loggar från ditt ASP.NET Core-program. 

Exemplet som vi ska använda här är ett [MVC-program](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) som `netcoreapp2.2`är mål. Du kan använda dessa instruktioner för alla ASP.NET Core-program.

## <a name="supported-scenarios"></a>Scenarier som stöds

[Application Insights SDK för ASP.net Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) kan övervaka dina program oavsett var eller hur de körs. Om ditt program körs och har nätverks anslutning till Azure, kan telemetri samlas in. Application Insights övervakning stöds överallt där .NET Core stöds. Support omfattar:
* **Operativsystem**: Windows, Linux eller Mac.
* **Värd metod**: I processen eller utanför processen. 
* **Distributions metod**: Ramverks beroende eller fristående.
* **Webb server**: IIS (Internet Information Server) eller Kestrel. 
* **Värd plattform**: Web Apps funktionen i Azure App Service, Azure VM, Docker, Azure Kubernetes service (AKS) och så vidare.
* **IDE**: Visual Studio, VS Code eller Command line.

## <a name="prerequisites"></a>Förutsättningar

- Ett fungerande ASP.NET Core program. Om du behöver skapa ett ASP.NET Core program följer du den här [ASP.net Core själv studie kursen](https://docs.microsoft.com/aspnet/core/getting-started/).
- En giltig Application Insights Instrumentation-nyckel. Den här nyckeln krävs för att skicka telemetri till Application Insights. Om du behöver skapa en ny Application Insights resurs för att hämta en Instrumentation-nyckel, se [skapa en Application Insights resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Aktivera Application Insights telemetri på Server sidan (Visual Studio)

1. Öppna projektet i Visual Studio.

    > [!TIP]
    > Om du vill kan du konfigurera käll kontroll för ditt projekt så att du kan spåra alla ändringar som Application Insights gör. Om du vill aktivera käll kontroll väljer du **fil** > **Lägg till i käll kontroll**.

2. Välj **projekt** > **Lägg till Application Insights Telemetry**.

3. Välj **Kom igång**. Detta urvals text kan variera beroende på vilken version av Visual Studio du har. Vissa tidigare versioner använder en **kostnads fri start** -knapp i stället.

4. Välj din prenumeration. Välj sedan **resurs** > **register**.

5. När du har lagt till Application Insights i projektet kontrollerar du att du använder den senaste stabila versionen av SDK. Gå till **Project** > **Hantera NuGet-paket** > **Microsoft. ApplicationInsights. AspNetCore**. Om du behöver väljer du **Uppdatera**.

     ![Skärm bild som visar var du kan välja Application Insightss paketet för uppdatering](./media/asp-net-core/update-nuget-package.png)

6. Om du har följt det valfria tipset och lagt till projektet i käll kontrollen går du till **Visa** > **team Explorer** > -**ändringar**. Välj sedan varje fil för att se en diff-vy av ändringarna som gjorts av Application Insights telemetri.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Aktivera Application Insights telemetri på Server sidan (inga Visual Studio)

1. Installera [Application Insights SDK NuGet-paketet för ASP.net Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Vi rekommenderar att du alltid använder den senaste stabila versionen. Hämta fullständiga versions anteckningar för SDK: n på [GitHub-lagrings platsen med öppen källkod](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    I följande kod exempel visas de ändringar som ska läggas till i projekt `.csproj` filen.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Lägg `services.AddApplicationInsightsTelemetry();`till i- `Startup` metoden i klassen, som i det här exemplet: `ConfigureServices()`

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

3. Konfigurera Instrumentation-tangenten.

    Även om du kan ange Instrumentation-nyckeln som ett argument `AddApplicationInsightsTelemetry`för, rekommenderar vi att du anger Instrumentation-nyckeln i konfigurationen. Följande kod exempel visar hur du anger en Instrumentation-nyckel i `appsettings.json`. `appsettings.json` Se till att kopieras till rotmappen för programmet under publiceringen.

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

    Alternativt kan du ange Instrumentation-nyckeln i någon av följande miljövariabler:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Exempel:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` Normalt anger Instrumentation-nyckeln för program som distribueras till Web Apps.

    > [!NOTE]
    > En Instrumentation-nyckel som anges i kod WINS över miljövariabeln `APPINSIGHTS_INSTRUMENTATIONKEY`, som vinner över andra alternativ.

## <a name="run-your-application"></a>Köra ditt program

Kör programmet och gör begär anden till det. Telemetri ska nu flöda till Application Insights. Application Insights SDK samlar automatiskt in följande telemetri.

|Begär Anden/beroenden |Information|
|---------------|-------|
|Begäranden | Inkommande webb förfrågningar till ditt program. |
|HTTP eller HTTPS | Anrop som görs `HttpClient`med. |
|SQL | Anrop som görs `SqlClient`med. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Anrop som görs med Azure Storage-klienten. |
|[EventHubs-klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 och senare. |
|[Service Bus-klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 och senare. |
|Azure Cosmos DB | Spåras automatiskt endast om HTTP/HTTPS används. Application Insights fångar inte TCP-läge. |

### <a name="performance-counters"></a>Prestandaräknare

Stöd för [prestanda räknare](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) i ASP.net Core är begränsat:

   * SDK-versioner 2.4.1 och senare samlar in prestanda räknare om programmet körs i Web Apps (Windows).
   * SDK-versioner 2.7.0-beta3 och senare samlar in prestanda räknare om programmet körs i Windows och mål `NETSTANDARD2.0` eller senare.
   * För program som är riktade till .NET Framework har alla versioner av SDK stöd för prestanda räknare.
 
Den här artikeln kommer att uppdateras när prestanda räknar stödet i Linux läggs till.

### <a name="ilogger-logs"></a>ILogger-loggar

[ILogger-loggar](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) av allvarlighets `Warning` grad eller större samlas automatiskt in i SDK-versioner 2.7.0-beta3 och senare.

### <a name="live-metrics"></a>Live-mått

Det kan ta några minuter innan telemetri börjar visas i portalen. För att snabbt se till att allt fungerar, är det bäst att använda [Live-mått](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) när du gör förfrågningar till det program som körs.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Aktivera telemetri på klient sidan för webb program

Föregående steg räcker för att hjälpa dig att börja samla in telemetri på Server sidan. Om programmet har komponenter på klient sidan följer du nästa steg för att börja samla in [användnings telemetri](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. I `_ViewImports.cshtml`lägger du till insprutning:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. I `_Layout.cshtml` `<head>` infogar `HtmlHelper` du i slutet av avsnittet men före andra skript. Om du vill rapportera anpassad JavaScript-telemetri från sidan, infogar du den efter det här kodfragmentet:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

De `.cshtml` fil namn som refereras tidigare är från en standard MVC-Programmall. Slutligen, om du vill aktivera övervakning på klient sidan för ditt program, måste JavaScript-kodfragmentet visas i `<head>` avsnittet på varje sida i programmet som du vill övervaka. Du kan utföra det här målet för den här program mal len genom att lägga `_Layout.cshtml`till JavaScript-kodfragmentet i. 

Om projektet inte innehåller `_Layout.cshtml`kan du fortfarande lägga till övervakning på [klient sidan](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Du kan göra detta genom att lägga till JavaScript-kodfragmentet i en motsvarande fil `<head>` som styr alla sidor i din app. Eller så kan du lägga till kodfragmentet på flera sidor, men den här lösningen är svår att underhålla och vi rekommenderar vanligt vis inte.

## <a name="configure-the-application-insights-sdk"></a>Konfigurera Application Insights SDK

Du kan anpassa Application Insights SDK för ASP.NET Core om du vill ändra standard konfigurationen. Användare av Application Insights ASP.NET SDK kan vara bekanta med att ändra konfigurationen genom att `ApplicationInsights.config` använda eller genom `TelemetryConfiguration.Active`att ändra. Du ändrar konfigurationen på olika sätt för ASP.NET Core. Lägg till ASP.NET Core SDK i programmet och konfigurera den med hjälp av ASP.NET Core inbyggd [beroende inmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Gör nästan alla konfigurations ändringar i `ConfigureServices()` -metoden för `Startup.cs` din klass, om du inte är inriktad på annat sätt. I följande avsnitt finns mer information.

> [!NOTE]
> I ASP.net Core program stöds inte ändring av konfiguration `TelemetryConfiguration.Active` genom att ändra.

### <a name="using-applicationinsightsserviceoptions"></a>Använda ApplicationInsightsServiceOptions

Du kan ändra några vanliga inställningar genom att skicka `ApplicationInsightsServiceOptions` till `AddApplicationInsightsTelemetry`, som i det här exemplet:

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

Mer information finns i konfigurations [bara inställningar i `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Samling

Application Insights SDK för ASP.NET Core stöder både fast priss ätt och anpassningsbar sampling. Adaptiv sampling är aktiverat som standard. 

Mer information finns i [Konfigurera adaptiv sampling för ASP.net Core program](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Lägger till TelemetryInitializers

Använd [telemetri](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) -initierare när du vill definiera globala egenskaper som skickas med all telemetri.

Lägg till alla `TelemetryInitializer` nya `DependencyInjection` i behållaren enligt följande kod. SDK: n hämtar automatiskt de `TelemetryInitializer` som läggs `DependencyInjection` till i behållaren.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Tar bort TelemetryInitializers

Telemetri-initierare finns som standard. Om du vill ta bort alla eller vissa telemetri-initierare använder du  följande exempel kod `AddApplicationInsightsTelemetry()`när du har anropat.

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

### <a name="adding-telemetry-processors"></a>Lägga till telemetri-processorer

Du kan lägga till anpassade telemetri- `TelemetryConfiguration` processorer i med hjälp av `AddApplicationInsightsTelemetryProcessor` tilläggs `IServiceCollection`metoden i. Du kan använda telemetri-processorer i [avancerade filtrerings scenarier](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) för att få mer direkt kontroll över vad som ingår eller exkluderas från Telemetrin som du skickar till den Application Insights tjänsten. Använd följande exempel.

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

Application Insights använder telemetri-moduler för att [automatiskt samla in användbar information](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) om vissa arbets belastningar utan ytterligare konfiguration.

Följande moduler för automatisk insamling är aktiverade som standard. Dessa moduler är ansvariga för automatisk insamling av telemetri. Du kan inaktivera eller konfigurera dem för att ändra deras standard beteende.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Om du vill konfigurera `TelemetryModule`standard använder du tilläggs metoden `ConfigureTelemetryModule<T>` på `IServiceCollection`, som du ser i följande exempel.

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

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-a-telemetry-channel"></a>Konfigurera en telemetri kanal

Standard kanalen är `ServerTelemetryChannel`. Du kan åsidosätta det som visas i följande exempel.

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

Om du vill inaktivera telemetri villkorligt och dynamiskt kan du lösa `TelemetryConfiguration` instansen med ASP.net Core beroende injektions behållare var som helst i koden och ange `DisableTelemetry` flaggan på den.

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

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hur kan jag spåra telemetri som inte samlas in automatiskt?

Hämta en instans av `TelemetryClient` med hjälp av konstruktorn för konstruktorn och `TrackXXX()` anropa metoden som krävs. Vi rekommenderar inte att du `TelemetryClient` skapar nya instanser i ett ASP.net Core program. En singleton-instans `TelemetryClient` av är redan registrerad `DependencyInjection` i behållaren, som delas `TelemetryConfiguration` med en rest av Telemetrin. Att skapa en `TelemetryClient` ny instans rekommenderas endast om den behöver en konfiguration som är separat från resten av Telemetrin. 

I följande exempel visas hur du spårar ytterligare telemetri från en kontrollant.

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

Mer information om anpassad data rapportering i Application Insights finns i [Application Insights anpassade mått-API-referens](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Vissa Visual Studio-mallar använde tilläggs metoden UseApplicationInsights () på IWebHostBuilder för att aktivera Application Insights. Är den här användningen fortfarande giltig?

Ja, aktivering av Application Insights med den här metoden är giltig. Den här tekniken används i Visual Studio onboarding och i Web Apps-tillägg. Vi rekommenderar dock att du `services.AddApplicationInsightsTelemetry()` använder eftersom det ger överbelastningar för att kontrol lera viss konfiguration. Båda metoderna gör samma sak internt, så om du inte behöver använda Anpassad konfiguration kan du anropa någon av metoderna.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Jag distribuerar mitt ASP.NET Core-program till Web Apps. Bör jag fortfarande aktivera Application Insights tillägget från Web Apps?

Om SDK installeras på bygg tid enligt den här artikeln behöver du inte aktivera [Application Insights-tillägget](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) från App Service portalen. Även om tillägget är installerat kommer det att stängas av när det upptäcker att SDK redan har lagts till i programmet. Om du aktiverar Application Insights från tillägget behöver du inte installera och uppdatera SDK. Men om du aktiverar Application Insights genom att följa anvisningarna i den här artikeln har du mer flexibilitet eftersom:

   * Application Insights telemetri fortsätter att fungera i:
       * Alla operativ system, inklusive Windows, Linux och Mac.
       * Alla publicerings lägen, inklusive oberoende eller Ramverks beroende.
       * Alla mål ramverk, inklusive den fullständiga .NET Framework.
       * Alla värd alternativ, inklusive Web Apps, virtuella datorer, Linux, behållare, Azure Kubernetes service och icke-Azure-värd.
   * Du kan se telemetri lokalt när du felsöker från Visual Studio.
   * Du kan spåra ytterligare anpassad telemetri med hjälp `TrackXXX()` av API: et.
   * Du har fullständig kontroll över konfigurationen.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan jag aktivera Application Insights övervakning med hjälp av verktyg som Statusövervakare?

Nej. [Statusövervakare](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) och [statusövervakare v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) stöder för närvarande endast ASP.NET 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Är Application Insights automatiskt aktiverat för mitt ASP.NET Core 2,0-program?

`Microsoft.AspNetCore.All` 2,0-Metapackage ingår Application Insights SDK (version 2.1.0). Om du kör programmet i Visual Studio-felsökaren aktiverar Visual Studio Application Insights och visar telemetri lokalt i IDE-objektet. Telemetri skickades inte till Application Insights tjänsten om inte en instrument nyckel angavs. Vi rekommenderar att du följer anvisningarna i den här artikeln för att aktivera Application Insights, även för 2,0-appar.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Fungerar alla funktioner om jag kör mitt program i Linux?

Ja. Funktions stödet för SDK är detsamma i alla plattformar, med följande undantag:

* Prestanda räknare stöds bara i Windows.
* Trots att `ServerTelemetryChannel` är aktiverat som standard, om programmet körs i Linux eller MacOS, skapar kanalen inte automatiskt en lokal lagringsmapp för att hålla telemetri tillfälligt om det uppstår nätverks problem. På grund av den här begränsningen försvinner telemetri när det uppstår tillfälliga nätverks-eller Server problem. Undvik det här problemet genom att konfigurera en lokal mapp för kanalen:

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

## <a name="open-source-sdk"></a>SDK för öppen källkod

[Läs och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Ta en titt på den här externa steg-för-steg-videon för att [konfigurera Application Insights med .net Core och Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) från grunden.
- Kolla ut den här externa stegvisa videon för att [konfigurera Application Insights med .net Core och Visual Studio Code](https://youtu.be/ygGt84GDync) från grunden.

## <a name="next-steps"></a>Nästa steg

* [Utforska användar flöden](../../azure-monitor/app/usage-flows.md) för att förstå hur användare navigerar i din app.
* [Konfigurera en ögonblicks bild samling](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) för att se status för källkod och variabler vid det tillfälle då ett undantag genereras.
* [Använd API: et](../../azure-monitor/app/api-custom-events-metrics.md) för att skicka egna händelser och mått för en detaljerad vy över appens prestanda och användning.
* Använd [tillgänglighets test](../../azure-monitor/app/monitor-web-app-availability.md) för att kontrol lera att din app ständigt är i hela världen.
* [Beroende inmatning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
