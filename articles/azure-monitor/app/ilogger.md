---
title: Utforska .NET-spårningsloggar med ILogger - Azure Application Insights
description: Exempel på hur du använder Azure Application Insights ILogger-providern med ASP.NET Core- och Console-program.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f40c1c1a8ee7f20c769a62e9746da43face4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276384"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider för .NET Core ILogger loggar

ASP.NET Core stöder ett loggnings-API som fungerar med olika typer av inbyggda och tredjepartsloggningsleverantörer. Loggning sker genom att anropa **Log()** eller en variant av den på *ILogger-instanser.* Den här artikeln visar hur du använder *ApplicationInsightsLoggerProvider* för att fånga ILogger loggar i konsolen och ASP.NET Core applikationer. I den här artikeln beskrivs också hur ApplicationInsightsLoggerProvider integreras med andra Application Insights telemetri.
Mer information finns [i Logga in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core-program

ApplicationInsightsLoggerProvider är aktiverat som standard i [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 (och senare) när du aktiverar regelbunden Application Insights-övervakning via någon av metoderna:

- Genom att anropa **metoden UseApplicationInsights** extension på IWebHostBuilder (nu föråldrad)
- Genom att anropa **metoden AddApplicationInsightsTelemetry** extension på IServiceCollection

ILogger loggar som ApplicationInsightsLoggerProvider fångar är föremål för samma konfiguration som alla andra telemetri som samlas in. De har samma uppsättning TelemetryInitializers och TelemetriProcessors, använder samma TelemetryChannel och korreleras och samplas på samma sätt som annan telemetri. Om du använder version 2.7.1 eller senare krävs ingen åtgärd för att fånga ILogger-loggar.

Endast *varning* eller högre ILogger-loggar (från alla [kategorier)](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)skickas som standard till Application Insights. Men du kan [använda filter för att ändra det här beteendet](#control-logging-level). Ytterligare steg krävs för att fånga ILogger loggar från **Program.cs** eller **Startup.cs**. (Se [Fånga ILogger-loggar från Startup.cs och Program.cs i ASP.NET Core-program](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Om du använder en tidigare version av Microsoft.ApplicationInsights.AspNet SDK eller om du bara vill använda ApplicationInsightsLoggerProvider utan någon annan Application Insights-övervakning använder du följande procedur:

1. Installera NuGet-paketet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Ändra **Program.cs** som visas här:

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
                   // Providing an instrumentation key here is required if you're using
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

Koden i steg 2 `ApplicationInsightsLoggerProvider`konfigurerar . Följande kod visar en exempelkontrollklass, som används `ILogger` för att skicka loggar. Loggarna fångas in av Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Fånga ILogger-loggar från Startup.cs och Program.cs i ASP.NET Core-appar

> [!NOTE]
> I ASP.NET Core 3.0 och senare är det `ILogger` inte längre möjligt att injicera i Startup.cs och Program.cs. Se https://github.com/aspnet/Announcements/issues/353 för mer information.

Den nya ApplicationInsightsLoggerProvider kan fånga loggar från tidigt i program-start pipeline. Även om ApplicationInsightsLoggerProvider aktiveras automatiskt i Application Insights (från och med version 2.7.1), har den inte en instrumenteringsnyckel inställd förrän senare på pipelinen. Så bara loggar från **Controller**/ andra klasser kommer att fångas. Om du vill fånga varje logg som börjar med **Program.cs** och **Startup.cs** själv måste du uttryckligen aktivera en instrumenteringsnyckel för ApplicationInsightsLoggerProvider. *TelemetryConfiguration* är inte heller helt konfigurerad när du loggar från **Program.cs** eller **Startup.cs** själv. Så dessa loggar kommer att ha en minsta konfiguration som använder InMemoryChannel, ingen sampling, och ingen standard telemetri initializers eller processorer.

Följande exempel visar den här funktionen med **Program.cs** och **Startup.cs**.

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
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exempel Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrera från den gamla ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK-versioner innan 2.7.1 stödde en loggningsleverantör som nu är föråldrad. Den här leverantören aktiverades via **tilläggsmetoden AddApplicationInsights()** för ILoggerFactory. Vi rekommenderar att du migrerar till den nya leverantören, vilket innebär två steg:

1. Ta bort *ILoggerFactory.AddApplicationInsights()* anrop från metoden **Startup.Configure()** för att undvika dubbel loggning.
2. Tillämpa alla filtreringsregler i kod igen, eftersom de inte kommer att respekteras av den nya leverantören. Överbelastningar av *ILoggerFactory.AddApplicationInsights()* tog minsta LogLevel eller filterfunktioner. Med den nya leverantören är filtrering en del av själva loggningsramverket. Det görs inte av Application Insights-leverantören. Så alla filter som tillhandahålls via *ILoggerFactory.AddApplicationInsights()* överbelastningar bör tas bort. Och filtreringsregler bör tillhandahållas genom att följa instruktioner för [kontrollloggningsnivå.](#control-logging-level) Om du använder *appsettings.json* för att filtrera loggning fortsätter den att fungera med den nya providern, eftersom båda använder samma leverantörsalias, *ApplicationInsights*.

Du kan fortfarande använda den gamla leverantören. (Det kommer att tas bort endast i en större version ändra till 3. *xx*.) Men vi rekommenderar att du migrerar till den nya leverantören av följande skäl:

- Den tidigare leverantören saknar stöd för [loggomfattningar](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). I den nya providern läggs egenskaper från scope automatiskt till som anpassade egenskaper i den insamlade telemetrin.
- Loggar kan nu fångas mycket tidigare i programmet startpipeline. Loggar från **program-** och **startklasserna** kan nu fångas in.
- Med den nya leverantören görs filtrering på själva ramnivå. Du kan filtrera loggar till Application Insights-providern på samma sätt som för andra leverantörer, inklusive inbyggda leverantörer som Konsol, Felsökning och så vidare. Du kan också använda samma filter på flera leverantörer.
- I ASP.NET Core (2.0 och senare) är det rekommenderade sättet att [aktivera loggningsleverantörer](https://github.com/aspnet/Announcements/issues/255) att använda tilläggsmetoder på ILoggingBuilder i **Program.cs** själv.

> [!Note]
> Den nya providern är tillgänglig för program som är inriktade på NETSTANDARD2.0 eller senare. Om ditt program inriktar sig på äldre .NET Core-versioner, till exempel .NET Core 1.1, eller om det är inriktat på .NET Framework, fortsätter du att använda den gamla providern.

## <a name="console-application"></a>Konsolprogram

> [!NOTE]
> Det finns en ny Application Insights SDK kallas [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) som kan användas för att aktivera Application Insights (ILogger och andra Application Insights telemetri) för alla konsolprogram. Vi rekommenderar att du använder detta paket och tillhörande instruktioner [härifrån](../../azure-monitor/app/worker-service.md).

Följande kod visar ett exempel konsolprogram som är konfigurerat för att skicka ILogger-spårningar till Application Insights.

Installerade paket:

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
        // Create the DI container.
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

I det här exemplet `Microsoft.Extensions.Logging.ApplicationInsights`används det fristående paketet . Som standard använder den här konfigurationen "minimum" TelemetryConfiguration för att skicka data till Application Insights. Minsta möjliga betyder att InMemoryChannel är den kanal som används. Det finns ingen provtagning och inga vanliga TelemetryInitializers. Detta kan åsidosättas för ett konsolprogram, vilket visas i följande exempel.

Installera det här ytterligare paketet:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

I följande avsnitt visas hur du åsidosätter standard telemetrikonfigureringen med hjälp av **tjänsterna. Konfigurera\<metoden TelemetryConfiguration>().** I det `ServerTelemetryChannel` här exemplet ställs in och sampling. Den lägger till en anpassad ITelemetryInitializer till TelemetryConfiguration.

```csharp
    // Create the DI container.
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

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Kontrollera loggningsnivå

Den ASP.NET Core *ILogger* infra har en inbyggd mekanism för att tillämpa [loggfiltrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). På så sätt kan du styra loggarna som skickas till varje registrerad leverantör, inklusive Application Insights-providern. Filtreringen kan göras antingen i konfiguration (vanligtvis med hjälp av en *appsettings.json-fil)* eller i kod. Denna möjlighet tillhandahålls av själva ramen. Den är inte specifik för application insights-providern.

Följande exempel gäller filterregler för ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Skapa filterregler i konfiguration med appsettings.json

För ApplicationInsightsLoggerProvider är provideraliaset `ApplicationInsights`. Följande avsnitt i *appsettings.json* instruerar loggningsleverantörer i allmänhet att logga på nivå *Varning* och högre. Det åsidosätter `ApplicationInsightsLoggerProvider` sedan till loggkategorier som börjar med "Microsoft" på nivå *Fel* och högre.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Skapa filterregler i kod

Följande kodavsnitt konfigurerar loggar för *Varning* och ovan från alla kategorier och för *Fel* och högre `ApplicationInsightsLoggerProvider`från kategorier som börjar med "Microsoft" som ska skickas till . Den här konfigurationen är densamma som i föregående avsnitt i *appsettings.json*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Vilka är de gamla och nya versionerna av ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) inkluderade en inbyggd ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), som aktiverades via **ILoggerFactory-tilläggsmetoder.** Denna leverantör är märkt föråldrad från version 2.7.1. Det kommer att tas bort helt i nästa större version förändring. [Själva Microsoft.ApplicationInsights.AspNetCore 2.6.1-paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) är inte föråldrat. Det krävs för att aktivera övervakning av begäranden, beroenden och så vidare.

Det föreslagna alternativet är det nya fristående paketet [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), som innehåller en förbättrad ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) och tilläggsmetoder på ILoggerBuilder för att aktivera den.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 är beroende av det nya paketet och aktiverar ILogger-hämtning automatiskt.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Varför visas vissa ILogger-loggar två gånger i Application Insights?

Duplicering kan uppstå om du har den äldre (nu föråldrade) versionen av `AddApplicationInsights` `ILoggerFactory`ApplicationInsightsLoggerProvider aktiverad genom att anropa . Kontrollera om **konfigurera metoden** har följande och ta bort den:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Om du får dubbel loggning när du `EnableDebugLogger` felsöker från Visual Studio anger du att den *är falsk* i koden som aktiverar Application Insights enligt följande. Duplicering och korrigering är bara relevant när du felsöker programmet.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Jag uppdaterade till [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1, och loggar från ILogger fångas in automatiskt. Hur stänger jag av den här funktionen helt?

Se avsnittet [Kontrollloggningsnivå](../../azure-monitor/app/ilogger.md#control-logging-level) för att se hur du filtrerar loggar i allmänhet. Om du vill inaktivera ApplicationInsightsLoggerProvider använder du: `LogLevel.None`

**I kod:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**I config:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Varför vissa ILogger loggar inte har samma egenskaper som andra?

Application Insights fångar och skickar ILogger-loggar med samma TelemetryConfiguration som används för alla andra telemetri. Men det finns ett undantag. Som standard är TelemetryConfiguration inte helt konfigurerad när du loggar från **Program.cs** eller **Startup.cs**. Loggar från dessa platser har inte standardkonfigurationen, så de kommer inte att köra alla TelemetryInitializers och TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Jag använder det fristående paketet Microsoft.Extensions.Logging.ApplicationInsights och jag vill logga ytterligare anpassad telemetri manuellt. Hur ska jag göra det?

När du använder det fristående `TelemetryClient` paketet, injiceras inte till DI-behållaren, så `TelemetryClient` du måste skapa en ny instans av och använda samma konfiguration som loggerprovidern använder, som följande kod visar. Detta säkerställer att samma konfiguration används för all anpassad telemetri samt telemetri från ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Om du använder Microsoft.ApplicationInsights.AspNetCore-paketet för att aktivera Application `TelemetryClient` Insights ändrar du den här koden för att komma direkt i konstruktorn. Ett exempel finns i [vanliga frågor och svar](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Vilken programinsiktstyp produceras från ILogger-loggar? Eller var kan jag se ILogger loggar i Application Insights?

ApplicationInsightsLoggerProvider fångar ILogger loggar och skapar TraceTelemetry från dem. Om ett undantagsobjekt skickas till metoden **Log()** på ILogger skapas *ExceptionTelemetry* i stället för TraceTelemetry. Dessa telemetriobjekt finns på samma ställen som andra TraceTelemetry- eller ExceptionTelemetry for Application Insights, inklusive portal, analys eller lokal felsökning av Visual Studio.

Om du föredrar att alltid skicka TraceTelemetry använder du det här kodavsnittet:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Jag har inte SDK installerat och jag använder Azure Web Apps-tillägget för att aktivera Application Insights för mina ASP.NET Core-program. Hur använder jag den nya leverantören? 

Tillägget Application Insights i Azure Web Apps använder den nya providern. Du kan ändra filtreringsreglerna i *filen appsettings.json* för ditt program.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Jag använder det fristående paketet Microsoft.Extensions.Logging.ApplicationInsights och aktiverar Application Insights-providern genom att ringa **builder. AddApplicationInsights("ikey")**. Finns det en möjlighet att få en instrumenteringsnyckel från konfigurationen?


Ändra Program.cs och appsettings.json enligt följande:

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

   Relevant avsnitt `appsettings.json`från:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Den här koden krävs endast när du använder en fristående loggningsleverantör. För regelbunden application insights-övervakning läses instrumentnyckeln in automatiskt från *konfigurationssökvägen ApplicationInsights: Instrumentationkey*. Appsettings.json ska se ut så här:

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

* [Logga in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET-spårningsloggar i Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
