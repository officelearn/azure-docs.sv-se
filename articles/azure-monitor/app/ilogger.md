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
ms.openlocfilehash: bd010193bf8f001d027ae80be9272ae30a0171c9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149976"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider för .NET Core ILogger loggar

ASP.NET Core stöder en API för loggning som fungerar med olika typer av loggning för inbyggda och från tredje part-leverantörer. Loggning görs genom att anropa **Log()** eller en variant av den på *ILogger* instanser. Den här artikeln visar hur du använder *ApplicationInsightsLoggerProvider* att samla in ILogger loggar i konsolen och ASP.NET Core-program. Den här artikeln beskriver också hur ApplicationInsightsLoggerProvider kan integreras med andra Application Insights telemetry.
Mer information finns i [loggning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core-program

ApplicationInsightsLoggerProvider är aktiverat som standard i [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 (och senare) när du aktiverar regelbundna Application Insights-övervakning på något av standarden metoder:
- Genom att anropa den **UseApplicationInsights** tilläggsmetod på IWebHostBuilder 
- Genom att anropa den **AddApplicationInsightsTelemetry** tilläggsmetod på IServiceCollection

ILogger loggar som samlar in ApplicationInsightsLoggerProvider omfattas av samma konfiguration som någon telemetri som samlats in. De har samma uppsättning TelemetryInitializers och TelemetryProcessors, Använd samma TelemetryChannel och korreleras och samplas på samma sätt som andra telemetri. Om du använder version 2.7.0-beta3 eller senare, ingen åtgärd krävs för att avbilda ILogger loggar.

Endast *varning* eller högre ILogger loggar (från alla kategorier) skickas till Application Insights som standard. Men du kan [Använd filter för att ändra det här beteendet](#control-logging-level). Ytterligare steg krävs för att avbilda ILogger loggar från **Program.cs** eller **Startup.cs**. (Se [ILogger samla in loggar från Startup.cs och Program.cs i ASP.NET Core-program](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Om du använder en tidigare version av Microsoft.ApplicationInsights.AspNet SDK eller vill du bara använda ApplicationInsightsLoggerProvider utan några andra Application Insights-övervakning, använder du följande procedur:

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

Koden i steg 2 konfigurerar `ApplicationInsightsLoggerProvider`. Följande kod visar ett exempel kontrollantklassen som använder `ILogger` skicka loggar. Loggarna som avbildas av Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Avbilda ILogger loggar från Startup.cs och Program.cs i ASP.NET Core-appar

Den nya ApplicationInsightsLoggerProvider kan samla in loggar från tidigt i pipeline för start av program. Även om ApplicationInsightsLoggerProvider aktiveras automatiskt i Application Insights (från och med version 2.7.0-beta3), har den inte en instrumenteringsnyckel anges tills senare i pipelinen. Därför bara loggar från **Controller**/ andra klasser som ska hämtas. Att samla in varje logg som börjar med **Program.cs** och **Startup.cs** själva, du måste uttryckligen aktivera en instrumenteringsnyckel för ApplicationInsightsLoggerProvider. Dessutom *TelemetryConfiguration* är inte helt konfigurerade när du loggar från **Program.cs** eller **Startup.cs** själva. Så har dessa loggar en lägsta konfiguration som använder InMemoryChannel, inga sampling och utan standardmoduler initieringar eller processorer.

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
            // Replace YourAppName with the namespace of your application's Program.cs.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs.
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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrera från gamla ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK-versioner innan 2.7.0-beta2 stöds en loggningsprovider som nu är föråldrade. Den här providern har aktiverats via den **AddApplicationInsights()** tilläggsmetod av ILoggerFactory. Vi rekommenderar att du migrerar till den nya providern som omfattar två steg:

1. Ta bort den *ILoggerFactory.AddApplicationInsights()* anropa från den **Startup.Configure()** metod för att undvika dubbla loggning.
2. Tillämpa alla filtreringsregler i kod, eftersom de inte kommer att respekteras av den nya providern. Overloads av *ILoggerFactory.AddApplicationInsights()* tog minsta LogLevel eller filtrera funktioner. Med den nya providern är filtrering en del av loggningsramverk själva. Det är inte klart av Application Insights-providern. Så de filter som tillhandahålls via *ILoggerFactory.AddApplicationInsights()* överlagringar som ska tas bort. Och filtreringsregler ska anges genom att följa den [Kontrollera loggningsnivån](#control-logging-level) instruktioner. Om du använder *appsettings.json* för att filtrera loggning, den kommer att fortsätta att arbeta med den nya providern eftersom båda använder samma provider-alias *ApplicationInsights*.

Du kan fortfarande använda den gamla-providern. (Det tas endast i en ändring av högre version 3. *xx*.) Men vi rekommenderar att du migrerar till den nya providern av följande skäl:

- Den tidigare providern saknar stöd för [logga scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). I den nya providern läggs automatiskt egenskaper från omfång som anpassade egenskaper till telemetrin som samlas in.
- Loggar kan nu hämtas mycket tidigare i Start-pipelinen för programmet. Loggar från den **programmet** och **Start** klasser nu samlas in.
- Med den nya providern görs filtrering på nivån framework. Du kan filtrera loggar till Application Insights-providern på samma sätt som för andra dataleverantörer, inklusive inbyggda providers som konsolen, felsökning och så vidare. Du kan också använda samma filter till flera leverantörer.
- I ASP.NET Core (2.0 och senare), det rekommenderade sättet att [aktivera loggning providers](https://github.com/aspnet/Announcements/issues/255) är genom att använda tilläggsmetoder på ILoggingBuilder i **Program.cs** själva.

> [!Note]
> Den nya providern är tillgänglig för program som är riktade till NETSTANDARD2.0 eller senare. Om ditt program används på äldre versioner för .NET Core, till exempel .NET Core 1.1, eller om den är inriktad på .NET Framework kan du fortsätta att använda den gamla-providern.

## <a name="console-application"></a>Konsolprogram

Följande kod visar ett konsolexempelprogram som är konfigurerad för att skicka ILogger spårningar till Application Insights.

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

Det här exemplet används det fristående paketet `Microsoft.Extensions.Logging.ApplicationInsights`. Som standard den här konfigurationen använder det ”minst” TelemetryConfiguration för att skicka data till Application Insights. Minsta innebär att InMemoryChannel den kanal som används. Det finns inga provtagning och inte standard TelemetryInitializers. Det här beteendet kan åsidosättas för ett konsolprogram som visas i följande exempel.

Installera den här ytterligare paket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

I följande avsnitt beskrivs hur du kan åsidosätta standardinställningen TelemetryConfiguration med hjälp av den **tjänster. Konfigurera<TelemetryConfiguration>()** metod. Det här exemplet ställer in `ServerTelemetryChannel` och sampling. En anpassad ITelemetryInitializer läggs till i TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Kontrollera loggningsnivån

ASP.NET Core *ILogger* infrastruktur har en inbyggd mekanism för att tillämpa [log filtrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). På så sätt kan du styra de loggar som skickas till varje registrerad provider, inklusive Application Insights-providern. Filtrering kan göras antingen i konfigurationen (vanligtvis med hjälp av en *appsettings.json* fil) eller i koden. Den här funktionen tillhandahålls av ramen för själva. Det är inte specifik för Application Insights-providern.

I följande exempel gäller filterregler ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Skapa statusfilterregler i konfigurationen med appsettings.json

För ApplicationInsightsLoggerProvider, provider-alias är `ApplicationInsights`. Följande avsnitt i *appsettings.json* konfigurerar loggar för *varning* och senare från alla kategorier och *fel* och senare från kategorier som börjar med ” Microsoft ”som ska skickas till `ApplicationInsightsLoggerProvider`.

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

Följande kodavsnitt konfigurerar loggar för *varning* och senare från alla kategorier och för *fel* och senare från kategorier som börjar med ”Microsoft” som ska skickas till `ApplicationInsightsLoggerProvider`. Den här konfigurationen är desamma som i föregående avsnitt i *appsettings.json*.

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

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) ingår en inbyggd ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider) som har aktiverats via  **ILoggerFactory** tilläggsmetoder. Den här providern har markerats föråldrad från version 2.7.0-beta2. Det kommer att tas bort helt i nästa huvudversion ändring. Den [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) själva paketet inte är föråldrad. Det krävs för att övervaka förfrågningar, beroenden och så vidare.

Föreslagna alternativ är det nya fristående paketet [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), som innehåller en förbättrad ApplicationInsightsLoggerProvider ( Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) och tilläggsmetoder på ILoggerBuilder för att aktivera den.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 får ett beroende på det nya paketet och möjliggör avbildning ILogger automatiskt.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Varför är vissa ILogger loggar visas två gånger i Application Insights?

Duplicering kan inträffa om du har den äldre (nu inaktuella) versionen av ApplicationInsightsLoggerProvider aktiverat genom att anropa `AddApplicationInsights` på `ILoggerFactory`. Kontrollera om din **konfigurera** metoden har följande och ta bort den:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Om det uppstår dubbla loggning när du felsöker från Visual Studio kan du ange `EnableDebugLogger` till *FALSKT* i koden som gör att Application Insights på följande sätt. Den här duplicering och fixa gäller endast när du felsöker programmet.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Jag har uppdaterat till [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 och loggar från ILogger kan samlas in automatiskt. Hur inaktiverar jag den här funktionen helt?

Se den [Kontrollera loggningsnivån](../../azure-monitor/app/ilogger.md#control-logging-level) vill se hur du filtrerar loggar i allmänhet. Inaktiverar ApplicationInsightsLoggerProvider använda `LogLevel.None`:

**I koden:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**I konfigurationen:**

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

Application Insights samlar in och skickar ILogger loggar med hjälp av samma TelemetryConfiguration som används för alla andra telemetri. Men det finns ett undantag. Som standard TelemetryConfiguration inte fullständigt ställs in när du loggar in från **Program.cs** eller **Startup.cs**. Loggar från dessa platser inte standardkonfigurationen så att de inte köra alla TelemetryInitializers och TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Jag använder det fristående paketet Microsoft.Extensions.Logging.ApplicationInsights och jag vill logga vissa ytterligare telemetri om anpassade manuellt. Hur ska jag göra?

När du använder det fristående paketet `TelemetryClient` matas inte till DI behållare, så måste du skapa en ny instans av `TelemetryClient` och använda samma konfiguration som logger-providern använder, som i följande kod. Detta säkerställer att samma konfiguration används för alla anpassad telemetri samt telemetri från ILogger.

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
> Om du använder Microsoft.ApplicationInsights.AspNetCore-paketet för att aktivera Application Insights kan ändra den här koden för att hämta `TelemetryClient` direkt i konstruktorn. Ett exempel finns i [här](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Vilken typ av telemetri för Application Insights skapas från ILogger loggar? Eller var hittar jag ILogger loggar i Application Insights?

ApplicationInsightsLoggerProvider samlar in ILogger loggar och skapar TraceTelemetry från dem. Om ett undantag-objekt har överförts till den **Log()** metoden på ILogger, *ExceptionTelemetry* skapas i stället för TraceTelemetry. Objekten telemetri finns på samma plats som andra TraceTelemetry eller ExceptionTelemetry för Application Insights, inklusive portal, analytics eller lokal Visual Studio-felsökaren.

Om du vill skicka alltid TraceTelemetry kan använda det här kodfragmentet: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Jag har installerat SDK: N och jag använda tillägget Azure Web Apps för att aktivera Application Insights för mitt ASP.NET Core-program. Hur kan jag använda den nya providern? 

Application Insights-tillägget i Azure Web Apps använder den gamla-providern. Du kan ändra filtreringsregler i den *appsettings.json* fil för ditt program. Om du vill dra nytta av den nya providern, Använd byggning instrumentation genom att utföra ett NuGet-beroende på SDK: N. Den här artikeln kommer att uppdateras när tillägget växlar till att använda den nya providern.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Jag är med hjälp av det fristående paketet Microsoft.Extensions.Logging.ApplicationInsights och aktivera Application Insights-providern genom att anropa **builder. AddApplicationInsights("ikey")**. Finns det ett alternativ för att få en instrumenteringsnyckel från konfigurationen?


Ändra Program.cs och appsettings.json på följande sätt:

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

   Relevant avsnitt från `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Den här koden krävs bara när du använder en fristående loggningsprovider. För vanliga Application Insights-övervakning, instrumenteringsnyckeln läses in automatiskt från konfigurationssökvägen *ApplicationInsights: Instrumentationkey*. AppSettings.JSON bör se ut så här:

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

* [Loggning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET spårningsloggar i Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
