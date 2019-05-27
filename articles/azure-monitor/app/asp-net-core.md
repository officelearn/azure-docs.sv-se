---
title: Azure Application Insights för ASP.NET Core | Microsoft Docs
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
ms.date: 05/21/2019
ms.author: mbullwin
ms.openlocfilehash: 8522b1d0a8f8466870966d3f11ce66f7bf15672b
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016374"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights för ASP.NET Core-program

Den här artikeln beskriver hur du aktiverar Application Insights för en [ASP.NET Core](https://docs.microsoft.com/aspnet/core) program. När du har slutfört instruktionerna i den här artikeln, ska Application Insights börja samla in förfrågningar, beroenden, undantag, prestandaräknare, pulsslag och loggar från ASP.NET Core-program. Exempelprogrammet som är en [MVC-appen](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) riktar in sig på `netcoreapp2.2`, men dessa anvisningar gäller för alla ASP.NET Core-program.

## <a name="supported-scenarios"></a>Scenarier som stöds

Den [Application Insights SDK (Software Development Kit) för ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) kan övervaka dina program oavsett var eller hur programmet körs. Om ditt program körs och har nätverksanslutning till Azure, kan telemetri samlas in. Det innebär att Application Insights-övervakning stöds överallt .NET Core stöds. Det här stödet innehåller alla operativsystem (Windows, Linux, Mac), värdmetoden (pågående vs out-of-process), distributionsmetod (framework-beroende eller fristående), webbserver (IIS, Kestrel), värdplattform (Azure Web Apps, Azure VM, Docker, Azure Kubernetes Service (AKS), och så vidare.) eller IDE (Visual Studio, VS Code kommandoraden).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En fungerande ASP.NET Core-program. Följ den [ASP.NET Core komma igång-guide](https://docs.microsoft.com/aspnet/core/getting-started/) att skapa ett ASP.NET Core-program, om det behövs.
- En giltig Application Insights instrumentationsnyckel, vilket krävs för att skicka någon telemetri till Application Insights-tjänsten. Följ den [och skapa en resurs instruktioner](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) att skapa en ny Application Insights-resurs, om det behövs och få en instrumenteringsnyckel.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Aktivera Application Insights från serversidan telemetri (Visual Studio)

1. Öppna projektet i Visual Studio.

    > [!TIP]
    > När du inte ett obligatoriskt steg, kan det vara bra att konfigurera källkontroll för ditt projekt så att du kan spåra alla ändringar som gjorts av Application Insights. Aktivera källa kontroll väljer **filen** > **Lägg till i källkontroll**.

2. Välj **projekt** > **Lägg till Application Insights Telemetry**.

3. Välj **börjar**. (Beroende på din version av Visual Studio kan texten kan variera något. Vissa tidigare versioner har en **starta kostnadsfri** knappen i stället.)

4. Välj din prenumeration och välj sedan **Resource** > **registrera**.

5. Kontrollera för att bekräfta att du använder den senaste stabila versionen av SDK: N när du lägger till Application Insights i projektet. Gå till **projekt** > **hantera NuGet-paket** > **Microsoft.ApplicationInsights.AspNetCore** > om det behövs väljer **Uppdatering**.

     ![Skärmbild av hantera NuGet-paketet skärmen med Application Insights-paketet som valts för uppdatering](./media/asp-net-core/update-nuget-package.png)

6. Om du har följt det valfria tipset och lagt till ditt projekt till källkontroll sedan går du till **visa** > **Team Explorer** > **ändringar** och Välj varje enskild fil för en diff-vy över ändringar som görs från att lägga till Application Insights telemetry.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Aktivera Application Insights från serversidan telemetri (utan Visual Studio)

1. Installera den [Application Insights SDK NuGet-paketet för ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Vi rekommenderar att du alltid använder den senaste stabila versionen. Fullständig viktig information för SDK finns på den [öppna GitHub-lagringsplatsen för källkod](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Följande kodavsnitt visar ändringarna som ska läggas till i projektets `.csproj` fil.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. Lägg till `services.AddApplicationInsightsTelemetry();` till den `ConfigureServices()` -metod i din `Startup` klass. Fullständigt exempel nedan.

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

    Även om det är möjligt att ange instrumenteringsnyckeln som ett argument till `AddApplicationInsightsTelemetry`, rekommenderar vi att du anger instrumenteringsnyckeln i konfigurationen. Följande visar hur du anger en instrumenteringsnyckel i `appsettings.json`. Kontrollera att `appsettings.json` har kopierats till rotmappen för programmet vid publicering.

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

    Alternativt kan kan instrumenteringsnyckeln också anges på något av följande miljövariabler.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Exempel:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` normalt används för att ange instrumenteringsnyckeln för program som distribueras till Azure Web Apps.

    > [!NOTE]
    > En instrumenteringsnyckel som anges i koden wins över miljövariabeln `APPINSIGHTS_INSTRUMENTATIONKEY`, vilket wins andra alternativ.

## <a name="run-your-application"></a>Kör programmet

 Kör ditt program och göra begäranden till den. Telemetri bör nu börjar flöda till Application Insights. Följande telemetri samlas in automatiskt av Application Insights SDK.

|Begäranden/beroenden |Information|
|---------------|-------|
|Begäranden | Inkommande webbegäranden till ditt program. |
|Http/Https | Anrop som görs med `HttpClient`. |
|SQL | Anrop som görs med `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Anrop som görs med Azure Storage-klienten. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 och senare. |
|[ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 och senare. |
|Azure Cosmos DB | Endast spåras automatiskt om du använder HTTP/HTTPS. TCP-läge samlas inte in av Application Insights. |

### <a name="performance-counters"></a>Prestandaräknare

Stöd för [prestandaräknare](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) i ASP.NET Core är begränsat till följande

   * SDK-version 2.4.1 och samlar in prestandaräknare ovan om programmet körs i Azure Web App (Windows)
   * SDK-version 2.7.0-beta3 och samlar in prestandaräknare ovan om programmet körs i Windows och riktar in sig på `NETSTANDARD2.0` eller högre.
   * För program som riktar in sig på .NET Framework, stöds prestandaräknare i alla versioner av SDK.
   * Den här artikeln kommer att uppdateras när du lägger till stöd för räknaren av prestanda i Linux.

### <a name="ilogger-logs"></a>ILogger loggar

[ILogger loggar](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) med allvarlighetsgrad `Warning` eller senare är automatiskt hämtade från SDK version 2.7.0-beta3 eller högre.

### <a name="live-metrics"></a>Live Metrics

Det kan ta några minuter för telemetri att starta visas i portalen. Att snabbt kontrollera om allt fungerar, är det bäst att använda [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), medan gör förfrågningar till programmet.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Aktivera telemetri för klientsidan för webbprogram

Stegen ovan är tillräckliga för att börja samla in telemetri på serversidan. Om ditt program har komponenter på klientsidan, följer du stegen nedan för att börja samla in [användningstelemetri](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) därifrån.

1. I `_ViewImports.cshtml`, lägga till inmatning:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. I `_Layout.cshtml`, infoga HtmlHelper i slutet av `<head>` avsnittet men före eventuella andra skript. Alla anpassade JavaScript-telemetri som du vill köra rapporten från sidan ska matas efter det här kodfragmentet:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Den `.cshtml` filnamnen som anges ovan är från en standardmall MVC-program. Slutligen korrekt aktivera övervakning på klientsidan för programmet om du behöver JavaScript-kodavsnitt måste finnas i den `<head>` delen av varje sida i programmet som du vill övervaka. För den här programmallen som att lägga till Javascript-kodavsnitt till `_Layout.cshtml` kommer ett effektivt sätt att uppnå det här målet. Om ditt projekt inte har den här specifika filen du kan fortfarande lägga till [övervakning på klientsidan –](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Du skulle måste antingen lägga till JavaScript till en motsvarande fil som styr den `<head>` på alla sidor i din app, eller också kan lägga till kodfragmentet i flera enskilda sidor även om det skulle vara svåra att underhålla och är vanligtvis inte Vi rekommenderar.

## <a name="configuring-application-insights-sdk"></a>Konfigurera Application Insights SDK

Application Insights SDK för ASP.NET Core kan anpassas för att ändra standardkonfigurationen. Användare av Application Insights ASP.NET SDK kanske är bekant med konfiguration med hjälp av `ApplicationInsights.config`, eller genom att ändra `TelemetryConfiguration.Active`. För ASP.NET Core görs konfiguration på olika sätt. ASP.NET Core SDK läggs till i programmet och konfigureras med hjälp av ASP.NET Core inbyggda [beroendeinmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Nästan alla konfigurationsändringar görs den `ConfigureServices()` -metoden för din `Startup.cs` class, om inget annat anges. Följ i avsnitten nedan om du vill veta mer.

> [!NOTE]
>  Ändra konfigurationen genom att ändra `TelemetryConfiguration.Active` rekommenderas inte i ASP.NET Core-program.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Konfigurera med ApplicationInsightsServiceOptions

Det går att ändra några vanliga inställningar genom att skicka `ApplicationInsightsServiceOptions` till `AddApplicationInsightsTelemetry`. Ett exempel visas nedan.

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

Den exakta listan över konfigurerbara inställningar i `ApplicationInsightsServiceOptions` finns [här](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Samling

Application Insights SDK för ASP.NET Core stöder både FixedRate och Adaptiv sampling. Adaptiv sampling är aktiverat som standard. Följ våra [vägledning för Adaptiv sampling](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)du vill veta hur du konfigurerar sampling för ASP.NET Core-program.

### <a name="adding-telemetryinitializers"></a>Att lägga till TelemetryInitializers

[Telemetri-initierare](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) används när du vill definiera globala egenskaper som skickas med all telemetri.

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

Anpassad telemetri processorer kan läggas till i `TelemetryConfiguration` med hjälp av metoden tillägget `AddApplicationInsightsTelemetryProcessor` på `IServiceCollection`. Telemetri processorer som används i [avancerad filtrering scenarier](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) för mer kontroll över vad är inkluderas eller uteslutas från telemetri som du skickar till Application Insights-tjänsten. Använd följande exempel.

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

Application Insights använder telemetri moduler som ett sätt att [Automatisk insamling av användbar information](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) om specifika arbetsbelastningar utan att kräva ytterligare konfiguration.

Följande Automatisk insamling moduler är aktiverade som standard och ansvarar för att automatiskt samla in telemetri. De kan inaktiveras och konfigureras om du vill ändra standardbeteendet.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Konfigurera eventuella standard `TelemetryModule`, använda metoden tillägget `ConfigureTelemetryModule<T>` på `IServiceCollection` som visas i exemplet nedan.

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

### <a name="configuring-telemetry-channel"></a>Konfigurera telemetri Channel

Standardkanal som används är den `ServerTelemetryChannel`. Den kan åsidosättas med följande exempel nedan.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Jag vill spåra ytterligare telemetri än telemetri som samlas in automatiskt. Hur gör jag det?

Skapa en instans av `TelemetryClient` av med hjälp av konstruktor inmatning och anropa de nödvändiga `TrackXXX()` metoden på den. Det rekommenderas inte att skapa en ny `TelemetryClient` instanser i ASP.NET Core-program som en singleton-instans av `TelemetryClient` är redan registrerat i den DI behållare som delar `TelemetryConfiguration` med resten av telemetrin. Skapa en ny `TelemetryClient` instans rekommenderas bara om den måste ha en separat konfigurationsfil från resten av telemetri. I följande exempel visas hur du spårar ytterligare telemetri från en domänkontrollant.

```csharp
using Microsoft.ApplicationInsights;

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

 Referera till [anpassade mått för Application Insights API-referens](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) en beskrivning av anpassade data rapportering i Application Insights.

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>En del Visual Studio-mallar används UseApplicationInsights() tilläggsmetod på IWebHostBuilder för att aktivera Application Insights. Den här användningen fortfarande är giltig?

Aktivera Application Insights med den här metoden är giltigt och används i Visual Studio registreringen och i Azure Web App-tillägg. Men det rekommenderas att använda `services.AddApplicationInsightsTelemetry()` eftersom det ger dig överlagringar för att styra viss konfiguration. Båda metoderna göra internt samma sak, om ingen anpassad konfiguration som ska användas, anropande antingen är bra.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Jag distribuera ASP.NET Core-program till Azure Web Apps. Bör jag fortfarande aktivera Application Insights-tillägget från Web Apps?

Om SDK: N installeras vid Byggtiden som visas i den här artikeln, finns det inget behov av att aktivera Application Insights-tillägget från App Service-portalen. Även om tillägget är installerat, kommer det tillbaka ut när det upptäcker att SDK: N har redan lagts till programmet. Aktivera Application Insights från tillägget behöver du inte installera och uppdatera SDK: N. Aktivera Application Insights enligt den här artikeln är dock mer flexibel skäl nedan.
   * Application Insights Telemetry fortsätter att fungera:
       * Alla operativsystem – Windows, Linux, Mac.
       * Alla publicera lägen - självständigt eller Framework-beroende.
       * Alla mål ramverk, inklusive fullständiga .NET Framework.
       * Alla Hosting alternativ – Azure Web APP, virtuella datorer, Linux, behållare, AKS, icke-Azure.
   * Telemetri kan ses lokalt, när du felsöker från Visual Studio.
   * Gör att spåra ytterligare anpassad telemetri med `TrackXXX()` API.
   * Du har fullständig kontroll över konfigurationen.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Kan jag aktivera Application Insights-övervakning med hjälp av verktyg som statusövervakaren?

Nej. [Statusövervakaren](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) och kommande har ersatts [statusövervakaren v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) stöder för närvarande ASP.NET 4.x endast.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>Jag har ett ASP.NET Core 2.0-program. Inte Application Insights aktiveras automatiskt utan mig gör ingenting?

`Microsoft.AspNetCore.All` 2.0 metapackage ingår Application Insights SDK (version 2.1.0), och om du kör programmet under Visual Studio-felsökaren Visual Studio gör det möjligt för Application Insights och visas telemetri som lokalt i IDE själva. Telemetri skickades inte till Application Insights-tjänsten, om inte en instrumenteringsnyckel anges uttryckligen. Vi rekommenderar att du följer anvisningarna i den här artikeln för att aktivera Application Insights, även för 2.0 appar.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Jag kör mitt program i Linux. Alla funktioner stöds i Linux samt?

* Ja. Funktioner som stöds för SDK är samma i alla plattformar, med följande undantag:

    * Prestandaräknare stöds inte ännu i icke-Windows.
    * Även om `ServerTelemetryChannel` är aktiverad som standard, om programmet körs i Linux eller MacOS, kanalen skapar inte automatiskt en lokal mapp för att hålla telemetri tillfälligt om nätverksproblem. Den här begränsningen gör telemetri till att gå förlorade om det finns tillfälliga nätverket eller serverproblem. Lösningen för det här problemet är för att konfigurera en lokal mapp för kanalen, enligt nedan.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

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

## <a name="open-source-sdk"></a>Öppen källkod-SDK
[Läsa och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Externa stegvisa video om [konfigurera Application Insights med .NET Core och Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) från grunden.

## <a name="next-steps"></a>Nästa steg
* [Utforska Användarflöden](../../azure-monitor/app/usage-flows.md) att förstå hur användarna navigerar i din app.
* [Konfigurera insamling av ögonblicksbild](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) att se status för källkoden och variabler för tillfället ett undantagsfel.
* [Använda API: et](../../azure-monitor/app/api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
* Använd [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) att kontrollera appens hela tiden från hela världen.
