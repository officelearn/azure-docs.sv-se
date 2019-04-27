---
title: Utforska .NET spårningsloggarna i Azure Application Insights med ILogger
description: Exempel på hur du använder Azure Application Insights ILogger providern med ASP.NET Core- och -konsolen.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901080"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider för .NET Core ILogger loggar

ASP.NET Core stöder en API för loggning som fungerar med olika typer av loggning för inbyggda och från tredje part-leverantörer. Loggning görs genom att anropa Log() eller en variant av den på `ILogger` instanser. Den här artikeln visar hur du använder `ApplicationInsightsLoggerProvider` att samla in `ILogger` loggar i både konsolen och ASP.NET Core-program. Den här artikeln beskriver också hur `ApplicationInsightsLoggerProvider` är integrerad med andra Application Insights telemetry.
Läs mer om inloggning av Asp.Net Core i [i den här artikeln](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core-program

Från och med [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 och senare, `ApplicationInsightsLoggerProvider` är aktiverat som standard när du aktiverar regelbundna Application Insights-övervakning med hjälp av antingen av standardmetoderna - av anropa `UseApplicationInsights` tilläggsmetod på IWebHostBuilder eller `AddApplicationInsightsTelemetry` tilläggsmetod på IServiceCollection. `ILogger` loggar som avbildas av `ApplicationInsightsLoggerProvider` omfattas av samma konfiguration som någon annan telemetri som samlas in. i.e de har samma uppsättning `TelemetryInitializer`s, `TelemetryProcessor`s, använder samma `TelemetryChannel`, och kommer vara korrelerade och samplas på samma sätt som alla andra telemetri.  Om du har den här versionen av SDK eller högre så ingen åtgärd krävs för att avbilda `ILogger` loggar.

Som standard bara `ILogger` loggar från `Warning` eller ovan (från alla kategorier) skickas till Application Insights. Det här beteendet kan ändras genom att använda filter enligt [här](#control-logging-level). Även ytterligare steg krävs om `ILogger` loggar från `Program.cs` eller `Startup.cs` är sparas enligt [här](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Om du använder en tidigare version av Microsoft.ApplicationInsights.AspNet SDK eller vill du bara använda ApplicationInsightsLoggerProvider, utan några andra Application Insights-övervakning, Följ stegen nedan.

1. Installera nuget-paketet.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. ändra `Program.cs` enligt nedan

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
            {
                // Providing an instrumentation key here is required if you are using
                // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                // or if you want to capture logs from early in the application startup
                // pipeline from Startup.cs or Program.cs itself.
                builder.AddApplicationInsights("ikey");

                // Optional: Apply filters to control what logs are sent to Application Insights.
                // The following configures LogLevel Information or above to be sent to
                // Application Insights for all categories.
                builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                 ("", LogLevel.Information);
            }
        );
}
```

Koden ovan kommer att konfigurera `ApplicationInsightsLoggerProvider`. Följande visar ett exempel kontrollantklassen som använder `ILogger` skicka loggar som avbildas av ApplicationInsights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Samla in ILogger loggar från Startup.cs Program.cs i Asp.Net Core-program

Med den nya ApplicationInsightsLoggerProvider är det möjligt att samla in loggar från tidigt i programmet Start pipelinen. Även om ApplicationInsightsLoggerProvider är aktiverat automatiskt Application Insights (från och med 2.7.0-beta3), har inte inställning av instrumentation förrän senare i pipelinen, så att endast loggar från domänkontrollanten / andra klasser som ska hämtas. Att samla in loggar från och med `Program.cs` och `Startup.cs` , en måste du uttryckligen aktivera ApplicationInsightsLoggerProvider med en instrumenteringsnyckel. Det är också viktigt att notera att `TelemetryConfiguration` fullständigt konfiguration när du loggar inte något från `Program.cs` eller `Startup.cs` själva, så dessa loggar kommer att använda en utan minsta konfiguration, som använder InMemoryChannel, inga sampling och ingen standard telemetri initieringar eller processorer.

Följande visar exempel på `Program.cs` och `Startup.cs` med den här funktionen.

#### <a name="example-programcs"></a>Exempel Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exempel Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Migrera från gamla ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK-versioner före 2.7.0-beta2, stöds en loggningsprovider är nu föråldrad. Den här providern har aktiverats med `AddApplicationInsights()` tilläggsmetod av `ILoggerFactory`. Den här providern är föråldrad och föreslås för användare för att migrera till den nya providern. Migreringen består av två steg.

1. Ta bort ILoggerFactory.AddApplicationInsights() anrop från `Startup.Configure()` metod för att undvika dubbla loggning.
2. Tillämpa alla filtreringsregler i koden som de inte uppfylls av den nya providern. Överlagringar av ILoggerFactory.AddApplicationInsights() tog minsta LogLevel eller filtrera funktioner. Med den nya providern filtrering är en del av loggningsramverk, och inte har gjort av Application Insights-providern. Så de filter som tillhandahålls via `ILoggerFactory.AddApplicationInsights()` överlagringar som ska tas bort och filtreringsregler ska tillhandahållas följa [dessa](#control-logging-level) instruktioner. Om du använder `appsettings.json` för att filtrera loggning, fortsätter att fungera med nya providern som båda använder samma Provider Alias - **ApplicationInsights**.

Gamla providern kan fortfarande användas (det är nu föråldrad och kommer att tas bort bara i huvudversion ändring 3.xx), migrerar till nyare providern rekommenderas på grund av följande orsaker.

1. Tidigare providern saknar stöd för [scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). I den nya providern läggs automatiskt egenskaper från omfång som anpassade egenskaper till telemetrin som samlas in.
2. Loggar kan nu hämtas mycket tidigare i Start-pipelinen för programmet. i.e Loggar från Program och Start klasser kan nu hämtas.
3. Med nya providern görs filtrering på nivån framework. Filtrering av loggar till Application Insights-providern kan göras i exakt samma sätt som för andra dataleverantörer, inklusive inbyggda providers som konsolen, felsökning och så vidare. Det är också möjligt att använda samma filter på flera leverantörer.
4. Den [rekommenderas](https://github.com/aspnet/Announcements/issues/255) sättet i ASP.NET Core (2.0 och senare) att aktivera loggning providers är genom att använda tilläggsmetoder på ILoggingBuilder i `Program.cs` själva.

> [!Note]
> Den nya providern är tillgänglig för program som riktar in sig på `NETSTANDARD2.0` eller högre. Om ditt program är inställd på äldre .NET Core-versioner som .NET Core 1.1 eller om inriktning på .NET Framework, kan du fortsätta att använda den gamla-providern.

## <a name="console-application"></a>Konsolprogram

Följande kod visar ett konsolexempelprogram som konfigurerats för att skicka `ILogger` spårningar till Application Insights.

Paket som är installerade:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

I exemplet ovan, det fristående paketet `Microsoft.Extensions.Logging.ApplicationInsights` används. Den här konfigurationen använder som standard minst `TelemetryConfiguration` för att skicka data till Application Insights. Minsta innebär att den kanal som används `InMemoryChannel`, inga provtagning och ingen standard TelemetryInitializers. Det här beteendet kan åsidosättas för ett konsolprogram som visas i exemplet nedan.

Installera den här ytterligare paket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

I följande avsnitt beskrivs hur du kan åsidosätta standardinställningen `TelemetryConfiguration` med hjälp av `services.Configure<TelemetryConfiguration>()` metod. Det här exemplet ställer in `ServerTelemetryChannel`sampling och lägger till en anpassad `ITelemetryInitializer` till den `TelemetryConfiguration`.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Kontrollera loggningsnivån

Asp.Net Core `ILogger` infrastruktur har inbyggd mekanism för att tillämpa [filtrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) i loggarna, vilket tillåter användare att kontrollera loggarna skickas till varje registrerad provider, inklusive Application Insights-providern. Den här typen av filtrering kan göras antingen i konfigurationen (vanligtvis via `appsettings.json` fil) eller i koden. Den här funktionen tillhandahålls av ramen för själva och är inte specifik för Application Insights-providern.

Exempel på tillämpa filterregler på ApplicationInsightsLoggerProvider anges nedan.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Skapa statusfilterregler i konfigurationen med appsettings.json

För ApplicationInsightsLoggerProvider, provider-alias är `ApplicationInsights`. Den under visas avsnitt i `appsettings.json` konfigurerar loggar `Warning` och senare från alla kategorier `Error` och senare från kategorier som börjar med ”Microsoft” som ska skickas till `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Skapa statusfilterregler i kod

Den under kodfragmentet konfigurerar loggar `Warning` och senare från alla kategorier `Error` och senare från kategorier som börjar med ”Microsoft” som ska skickas till `ApplicationInsightsLoggerProvider`. Den här konfigurationen är samma som ovan konfig i `appsettings.json`.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

*1. Vad är den gamla och nya ApplicationInsightsLoggerProvider?*

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) levereras med en inbyggd ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), som aktiverades med ILoggerFactory tilläggsmetoder. Den här providern är markerade som föråldrade från och med 2.7.0-beta2 och kommer att tas bort helt i nästa huvudversion ändring. [Detta](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) paketera själva inte föråldrade och krävs för att aktivera övervakning av förfrågningar, beroenden osv.

* Föreslagna alternativ är det nya fristående paketet [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), som innehåller en förbättrad ApplicationInsightsLoggerProvider ( Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider), och tillägg metoder i ILoggerBuilder för att aktivera den.

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 och senare tar ett beroende på ovanstående paketet och aktiverar `ILogger` avbilda automatiskt.

*2. Jag ser vissa `ILogger` loggar visas två gånger i Application Insights?*

* Duplicering kan inträffa om du har den äldre (nu inaktuella) versionen av `ApplicationInsightsLoggerProvider` aktiverat genom att anropa `AddApplicationInsights` på `ILoggerFactory`. Kontrollera om din `Configure` metoden har följande och ta bort den.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Om du upplever dubbla loggning när du felsöker från Visual Studio, sedan ändra koden för att aktivera Application Insights på följande sätt genom att ange `EnableDebugLogger` ska vara falskt. Det här duplicering problemet och åtgärda gäller endast när du felsöker programmet.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. Jag har uppdaterat till [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 och jag ser nu som loggar från `ILogger` kan samlas in automatiskt. Hur kan jag har stängt av den här funktionen helt?*

* Se [detta](../../azure-monitor/app/ilogger.md#control-logging-level) avsnittet om du vill veta hur du filtrerar loggar i allmänhet. Inaktiverar ApplicationInsightsLoggerProvider använda `LogLevel.None` för den.

  I koden

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  I konfigurationen

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Jag ser vissa `ILogger` loggar inte har samma egenskaper som andra?*

* Application Insights-avbildningar och skickar `ILogger` loggar med samma `TelemetryConfiguration` används för alla andra telemetri. Det finns ett undantag till den här regeln. Standard `TelemetryConfiguration` fullständigt konfiguration när du loggar inte något från `Program.cs` eller `Startup.cs` själva, så att loggar från dessa platser har inte standardkonfigurationen och kan därför inte körs alla de `TelemetryInitializer`s och `TelemetryProcessor`s.

*5. Jag använder det fristående paketet Microsoft.Extensions.Logging.ApplicationInsights och jag vill logga vissa ytterligare telemetri om anpassade manuellt. Hur ska jag göra?*

* När du använder det fristående paketet `TelemetryClient` matas inte till DI behållare, så att användare förväntas behöva skapa en ny instans av `TelemetryClient` genom att använda samma konfiguration som används av logger-leverantör som visas nedan. Detta säkerställer att samma konfiguration används för anpassad telemetri, samt de som hämtats från ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Observera att, om paketet Microsoft.ApplicationInsights.AspNetCore paketet används för att aktivera Application Insights, sedan i ovanstående exempel bör ändras för att få `TelemetryClient` direkt i konstruktorn. Se [detta](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions) för fullständigt exempel.


*6. Vilken typ av telemetri för Application Insights skapas från `ILogger` loggar? eller var hittar jag `ILogger` loggar i Application Insights?*

* Samlar in ApplicationInsightsLoggerProvider `ILogger` loggar och skapar `TraceTelemetry` från den. Om ett undantag objekt skickas till metoden Log() på ILogger, sedan i stället för `TraceTelemetry`, en `ExceptionTelemetry` har skapats. Objekten telemetri finns på samma platser som någon annan `TraceTelemetry` eller `ExceptionTelemetry` för Application Insights, inklusive portal, analytics eller lokal Visual Studio-felsökaren.
Om du vill skicka alltid `TraceTelemetry`, använda kodfragmentet ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*7. Jag behöver SDK är installerat, och jag använder Azure Web App Extension för att aktivera Application Insights för Mina Asp.Net Core-program. Hur kan jag använda den nya providern?*

* Application Insights-tillägget i Azure Web App använder den gamla-providern. Filtreringsregler kan ändras i `appsettings.json` för ditt program. Om du vill dra nytta av den nya providern kan du använda byggning instrumentation genom att nuget beroende på SDK: N. Det här dokumentet kommer att uppdateras när tillägget växlar till att använda den nya providern.

*8. Jag är med hjälp av det fristående paketet Microsoft.Extensions.Logging.ApplicationInsights och aktivera Application Insights-providern genom att anropa builder. AddApplicationInsights("ikey"). Finns det ett alternativ för att hämta instrumenteringsnyckeln från konfigurationen?*


* Ändra `Program.cs` och `appsettings.json` enligt nedan.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging((hostingContext, logging) =>
            {
                // hostingContext.HostingEnvironment can be used to determine environments as well.
                var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                logging.AddApplicationInsights(appInsightKey);
            });
}
```

Relevant avsnitt från `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

Koden ovan krävs endast när du använder fristående loggningsprovider. För vanliga Application Insights-övervakning, instrumenteringsnyckeln läses in automatiskt från konfigurationssökvägen `ApplicationInsights:Instrumentationkey` och `appsettings.json` bör se ut som nedan.

```json
{
  "ApplicationInsights":
    {
        "Instrumentationkey":"putrealikeyhere"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om:

* [Loggning i Asp.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET spårningsloggar i Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
