---
title: Azure Application Insights för ASP.NET Core-program | Microsoft Docs
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
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: 7fe5a4f5a5d1d254918f1b4f997acfb9cf67a75b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272451"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights för ASP.NET Core-program

Den här artikeln beskriver hur du aktiverar Application Insights för en [ASP.NET Core](https://docs.microsoft.com/aspnet/core) program. När du har slutfört instruktionerna i den här artikeln, Application Insights samlar in förfrågningar, beroenden, undantag, prestandaräknare, pulsslag och loggar från ASP.NET Core-program. 

Exemplet använder vi här är en [MVC-appen](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) som riktar in sig `netcoreapp2.2`. Du kan använda de här anvisningarna för alla ASP.NET Core-program.

## <a name="supported-scenarios"></a>Scenarier som stöds

Den [Application Insights SDK för ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) kan övervaka dina program oavsett var eller hur de körs. Om ditt program körs och har nätverksanslutning till Azure, kan telemetri samlas in. Application Insights-övervakning stöds överallt .NET Core stöds. Har stöd för omfattar:
* **Operativsystem**: Windows, Linux och Mac.
* **Som är värd för metoden**: Pågående eller processer. 
* **Distributionsmetod**: Framework beroende eller fristående.
* **Webbservern**: IIS (Internet informationsserver) eller Kestrel. 
* **Värdplattform**: Funktionen Web Apps i Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS) och så vidare.
* **IDE**: Visual Studio, VS Code eller kommandoraden.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En fungerande ASP.NET Core-program. Om du vill skapa ett ASP.NET Core-program kan du följa den här [ASP.NET Core-självstudien](https://docs.microsoft.com/aspnet/core/getting-started/).
- En giltig Application Insights-instrumenteringsnyckeln. Den här nyckeln krävs för att skicka någon telemetri till Application Insights. Om du vill skapa en ny Application Insights-resurs för att få en instrumentation nyckel finns i [skapar en Application Insights-resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Aktivera Application Insights från serversidan telemetri (Visual Studio)

1. Öppna projektet i Visual Studio.

    > [!TIP]
    > Om du vill kan kan du konfigurera källkontroll för ditt projekt så att du kan spåra alla ändringar som gör Application Insights. Aktivera källkontroll **filen** > **Lägg till i källkontroll**.

2. Välj **projekt** > **Lägg till Application Insights Telemetry**.

3. Välj **börjar**. Text för det här alternativet kan variera beroende på din version av Visual Studio. Vissa tidigare versioner använder en **starta kostnadsfri** knappen i stället.

4. Välj din prenumeration. Välj sedan **Resource** > **registrera**.

5. Kontrollera för att bekräfta att du använder den senaste stabila versionen av SDK: N när du lägger till Application Insights i projektet. Gå till **projekt** > **hantera NuGet-paket** > **Microsoft.ApplicationInsights.AspNetCore**. Om du behöver kan du välja **uppdatering**.

     ![Skärmbild som visar var du väljer Application Insights-paketet för uppdatering](./media/asp-net-core/update-nuget-package.png)

6. Om du har följt det valfria tipset och lagt till ditt projekt till källkontroll, gå till **visa** > **Team Explorer** > **ändringar**. Välj sedan varje fil för att se en diff-vy över de ändringar som gjorts av Application Insights telemetry.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Aktivera Application Insights från serversidan telemetri (inga Visual Studio)

1. Installera den [Application Insights SDK NuGet-paketet för ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Vi rekommenderar att du alltid använder den senaste stabila versionen. Hitta fullständiga viktig information för SDK på den [GitHub-lagringsplatsen för öppen källkod](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Följande kodexempel visar ändringarna som ska läggas till i projektets `.csproj` fil.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Lägg till `services.AddApplicationInsightsTelemetry();` till den `ConfigureServices()` -metod i din `Startup` klass, som i följande exempel:

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

3. Konfigurera instrumenteringsnyckeln.

    Även om du kan ange instrumenteringsnyckeln som ett argument för `AddApplicationInsightsTelemetry`, rekommenderar vi att du anger instrumenteringsnyckeln i konfigurationen. Följande kodexempel visar hur du anger en instrumenteringsnyckel i `appsettings.json`. Kontrollera att `appsettings.json` har kopierats till rotmappen för programmet under publiceringen.

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

    Du kan också ange instrumenteringsnyckeln i något av följande miljövariabler:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Exempel:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Normalt `APPINSIGHTS_INSTRUMENTATIONKEY` anger instrumenteringsnyckeln för program som distribueras till Web Apps.

    > [!NOTE]
    > En instrumenteringsnyckel som anges i koden wins över miljövariabeln `APPINSIGHTS_INSTRUMENTATIONKEY`, vilket wins andra alternativ.

## <a name="run-your-application"></a>Köra ditt program

Kör din App och göra begäranden till den. Telemetri ska nu flöda till Application Insights. Application Insights SDK samlar automatiskt in följande telemetri.

|Begäranden/beroenden |Information|
|---------------|-------|
|Begäranden | Inkommande webbegäranden till ditt program. |
|HTTP eller HTTPS | Anrop som görs med `HttpClient`. |
|SQL | Anrop som görs med `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Anrop som görs med Azure Storage-klienten. |
|[SDK för EventHubs-klient](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 och senare. |
|[ServiceBus client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 och senare. |
|Azure Cosmos DB | Spåras automatiskt endast om du använder HTTP/HTTPS. Application Insights inte samla in TCP-läge. |

### <a name="performance-counters"></a>Prestandaräknare

Stöd för [prestandaräknare](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) i ASP.NET Core är begränsat:

   * SDK-versioner 2.4.1 och senare samla in prestandaräknare om programmet körs i Web Apps (Windows).
   * SDK-versioner 2.7.0-beta3 och senare samla in prestanda prestandaräknare om programmet körs i Windows och mål `NETSTANDARD2.0` eller senare.
   * Alla versioner av SDK: N stöder prestandaräknare för program som riktar in sig på .NET Framework.
 
Den här artikeln kommer att uppdateras när du lägger till stöd för räknaren av prestanda i Linux.

### <a name="ilogger-logs"></a>ILogger loggar

[ILogger loggar](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) med allvarlighetsgrad `Warning` eller större fångas automatiskt i SDK-versioner 2.7.0-beta3 och senare.

### <a name="live-metrics"></a>Live Metrics

Det kan ta några minuter innan telemetri startar visas i portalen. För att snabbt kontrollera att allt fungerar, är det bäst att använda [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) när du gör förfrågningar till programmet.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Aktivera telemetri för klientsidan för webbprogram

Föregående steg är tillräckliga för att börja samla in telemetri på serversidan. Om programmet har komponenter på klientsidan, Följ stegen nedan börjar samla in [användningstelemetri](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. I `_ViewImports.cshtml`, lägga till inmatning:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. I `_Layout.cshtml`, infoga `HtmlHelper` i slutet av den `<head>` avsnittet men före eventuella andra skript. Om du vill rapportera eventuella anpassade JavaScript-telemetri från sidan kunna matas in efter det här kodfragmentet:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Den `.cshtml` filnamnen som refererar till tidigare är från en standardmall MVC-program. Slutligen, om du vill aktivera korrekt övervakning på klientsidan – för ditt program, JavaScript-kodavsnitt måste visas i den `<head>` delen av varje sida i programmet som du vill övervaka. Du kan göra det här målet för den här programmallen genom att lägga till JavaScript-kodavsnitt till `_Layout.cshtml`. 

Om ditt projekt inte innehåller `_Layout.cshtml`, du kan fortfarande lägga till [övervakning på klientsidan –](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Du kan göra detta genom att lägga till JavaScript-kodavsnitt till en motsvarande-fil som styr den `<head>` på alla sidor i din app. Eller du kan lägga till kodfragmentet flera sidor, men den här lösningen är svåra att underhålla och allmänt rekommenderar vi inte.

## <a name="configure-the-application-insights-sdk"></a>Konfigurera Application Insights SDK

Du kan anpassa Application Insights SDK för ASP.NET Core att ändra standardkonfigurationen. Användare av Application Insights ASP.NET SDK kanske är bekant med att ändra konfigurationen med hjälp av `ApplicationInsights.config` eller genom att ändra `TelemetryConfiguration.Active`. Du kan ändra konfigurationen på olika sätt för ASP.NET Core. Lägg till ASP.NET Core SDK i programmet och konfigurera den med hjälp av ASP.NET Core inbyggda [beroendeinmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Göra nästan alla ändringar i den `ConfigureServices()` -metoden för din `Startup.cs` class, såvida inte du annars är skickas. I följande avsnitt ger mer information.

> [!NOTE]
> I ASP.NET Core-program, ändra konfiguration genom att ändra `TelemetryConfiguration.Active` rekommenderas inte.

### <a name="using-applicationinsightsserviceoptions"></a>Using ApplicationInsightsServiceOptions

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

Mer information finns i den [konfigurerbara inställningar i `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Samling

Application Insights SDK för ASP.NET Core stöder både fast räntesats och Adaptiv sampling. Adaptiv sampling är aktiverat som standard. 

Mer information finns i [konfigurera Adaptiv sampling för ASP.NET Core-program](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Att lägga till TelemetryInitializers

Använd [telemetri-initierare](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) när du vill definiera globala egenskaper som skickas med all telemetri.

Lägg till någon ny `TelemetryInitializer` till den `DependencyInjection` behållare som visas i följande kod. SDK: N automatiskt över någon `TelemetryInitializer` som läggs till i `DependencyInjection` behållare.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Ta bort TelemetryInitializers

Telemetri-initierare finns som standard. Att ta bort alla eller specifika telemetri-initierare, Använd följande exempelkod *när* du anropar `AddApplicationInsightsTelemetry()`.

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

### <a name="adding-telemetry-processors"></a>Lägga till processorer för telemetri

Du kan lägga till anpassad telemetri processorer som `TelemetryConfiguration` med hjälp av metoden tillägget `AddApplicationInsightsTelemetryProcessor` på `IServiceCollection`. Du använder telemetri processorer i [avancerad filtrering scenarier](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) för mer kontroll över vad har inkluderas eller uteslutas från telemetri som du skickar till Application Insights-tjänsten. Använd följande exempel.

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

Application Insights använder telemetri-moduler [automatiskt samla in värdefull information](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) om specifika arbetsbelastningar utan ytterligare konfiguration.

Automatisk insamling följande moduler är aktiverade som standard. Dessa moduler ansvarar för att automatiskt samla in telemetri. Du kan inaktivera eller konfigurera dem för att ändra sina standardbeteendet.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Konfigurera eventuella standard `TelemetryModule`, använda metoden tillägget `ConfigureTelemetryModule<T>` på `IServiceCollection`, enligt följande exempel.

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

### <a name="configuring-a-telemetry-channel"></a>Konfigurera en telemetri-kanal

Standardkanalen är `ServerTelemetryChannel`. Du kan åsidosätta den som visas i följande exempel.

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

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hur kan jag för att spåra telemetri som samlas inte automatiskt?

Hämta en instans av `TelemetryClient` av med hjälp av konstruktor inmatning och anropa de nödvändiga `TrackXXX()` metoden på den. Vi rekommenderar inte att skapa nya `TelemetryClient` instanser i ett ASP.NET Core-program. En singleton-instans av `TelemetryClient` är redan registrerat i den `DependencyInjection` behållare som delar `TelemetryConfiguration` med resten av telemetrin. Skapa en ny `TelemetryClient` instans rekommenderas bara om det behöver en konfiguration som är separat från resten av telemetrin. 

I följande exempel visas hur du spårar ytterligare telemetri från en domänkontrollant.

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

Läs mer om anpassade data rapportering i Application Insights [anpassade mått för Application Insights API-referens](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>En del Visual Studio-mallar används tilläggsmetod UseApplicationInsights() på IWebHostBuilder för att aktivera Application Insights. Den här användningen fortfarande är giltig?

Ja, aktivera Application Insights med den här metoden är giltig. Den här tekniken används i Visual Studio-integrering och Web Apps-tillägg. Men vi rekommenderar att du använder `services.AddApplicationInsightsTelemetry()` eftersom det ger överlagringar för att styra viss konfiguration. Båda metoderna gör samma sak internt, så om du inte behöver tillämpa anpassad konfiguration, kan du anropa någon av metoderna.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Jag distribuera ASP.NET Core-program till Web Apps. Bör jag fortfarande aktivera Application Insights-tillägget från Web Apps?

Om SDK: N installeras vid Byggtiden som visas i den här artikeln, behöver du inte aktivera Application Insights-tillägget från App Service-portalen. Även om tillägget är installerat, kommer det tillbaka ut när det upptäcker att SDK: N har redan lagts till programmet. Om du aktiverar Application Insights från tillägget kan behöver du inte installera och uppdatera SDK: N. Men om du aktiverar Application Insights genom att följa instruktionerna i den här artikeln har du mer flexibilitet eftersom:

   * Application Insights telemetry fortsätter att fungera:
       * Alla operativsystem, inklusive Windows, Linux och Mac.
       * Alla publicera lägen, inklusive fristående eller framework beroende.
       * Alla mål ramverk, inklusive fullständiga .NET Framework.
       * Alla värdalternativ, inklusive Web Apps, virtuella datorer, Linux, behållare, Azure Kubernetes Service och som är värd för icke-Azure.
   * Du kan se telemetri lokalt när du felsöker från Visual Studio.
   * Du kan spåra ytterligare anpassad telemetri med hjälp av den `TrackXXX()` API.
   * Du har fullständig kontroll över konfigurationen.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan jag aktivera Application Insights-övervakning med hjälp av verktyg som statusövervakaren?

Nej. [Statusövervakaren](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) och [statusövervakaren v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) för närvarande stöder ASP.NET 4.x endast.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights aktiveras automatiskt för mitt ASP.NET Core 2.0-program?

Den `Microsoft.AspNetCore.All` 2.0 metapackage ingår Application Insights SDK (version 2.1.0). Om du kör programmet under Visual Studio-felsökaren kan Application Insights i Visual Studio och visas telemetri som lokalt i IDE själva. Telemetri har inte skickats till Application Insights-tjänsten, såvida inte en instrumenteringsnyckel har angetts. Vi rekommenderar att du följer anvisningarna i den här artikeln för att aktivera Application Insights, även för 2.0 appar.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Om jag kör mitt program i Linux alla funktioner stöds?

Ja. Funktioner som stöds för SDK är samma i alla plattformar, med följande undantag:

* Prestandaräknare stöds endast i Windows.
* Även om `ServerTelemetryChannel` är aktiverad som standard, om programmet körs i Linux eller MacOS, kanalen automatiskt skapar inte en lokal mapp för att hålla telemetri tillfälligt om nätverksproblem. På grund av den här begränsningen kan förloras telemetri när det finns tillfälliga nätverks- eller problem. Undvik problemet genom att konfigurera en lokal mapp för kanalen:

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

## <a name="open-source-sdk"></a>Öppen källkod-SDK

[Läsa och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Kolla in den här externa stegvisa videon till [konfigurera Application Insights med .NET Core och Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) från grunden.
- Kolla in den här externa stegvisa videon till [konfigurera Application Insights med .NET Core och Visual Studio Code](https://youtu.be/ygGt84GDync) från grunden.

## <a name="next-steps"></a>Nästa steg

* [Utforska användarflöden](../../azure-monitor/app/usage-flows.md) att förstå hur användarna navigerar i din app.
* [Konfigurera en ögonblicksbild samling](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) att se status för källkoden och variabler för tillfället ett undantagsfel.
* [Använda API: et](../../azure-monitor/app/api-custom-events-metrics.md) att skicka dina egna händelser och mått för en detaljerad vy av appens prestanda och användning.
* Använd [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) att kontrollera appens hela tiden från hela världen.
