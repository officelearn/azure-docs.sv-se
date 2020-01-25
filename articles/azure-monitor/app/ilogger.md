---
title: Utforska .NET-spårnings loggar med ILogger Azure Application-insikter
description: Exempel på användning av Azure Application Insights ILogger-providern med ASP.NET Core-och konsol program.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: b538196467ba1d69e679a111ca313f922738b048
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716020"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider för .NET Core ILogger-loggar

ASP.NET Core stöder en loggnings-API som fungerar med olika typer av inbyggda och utomstående loggnings leverantörer. Loggning görs genom att anropa **log ()** eller en variant av den på *ILogger* -instanser. Den här artikeln visar hur du använder *ApplicationInsightsLoggerProvider* för att avbilda ILogger-loggar i-konsolen och ASP.net Core program. Den här artikeln beskriver också hur ApplicationInsightsLoggerProvider integreras med andra Application Insights telemetri.
Mer information finns i [Logga in ASP.net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core program

ApplicationInsightsLoggerProvider är aktiverat som standard i [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 (och senare) när du aktiverar regelbunden Application Insights övervakning via någon av standard metoderna:

- Genom att anropa **UseApplicationInsights** -tilläggs metoden på IWebHostBuilder
- Genom att anropa **AddApplicationInsightsTelemetry** -tilläggs metoden på IServiceCollection

ILogger loggar som ApplicationInsightsLoggerProvider-avbildningar omfattas av samma konfiguration som andra telemetri som samlas in. De har samma uppsättning TelemetryInitializers och TelemetryProcessors, använder samma TelemetryChannel och korreleras och samplas på samma sätt som andra telemetri. Om du använder version 2.7.1 eller senare krävs ingen åtgärd för att avbilda ILogger-loggar.

Endast *varnings* -eller högre ILogger-loggar (från alla [Kategorier](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)) skickas till Application Insights som standard. Men du kan [använda filter för att ändra det här beteendet](#control-logging-level). Ytterligare steg krävs för att avbilda ILogger-loggar från **program.cs** eller **startup.cs**. (Mer information finns i [fånga in ILogger-loggar från startup.CS och program.cs i ASP.net Core-program](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Använd följande procedur om du använder en tidigare version av Microsoft. ApplicationInsights. ASPNET SDK eller om du bara vill använda ApplicationInsightsLoggerProvider utan någon annan Application Insights övervakning:

1. Installera NuGet-paketet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Ändra **program.cs** som visas här:

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

Koden i steg 2 konfigurerar `ApplicationInsightsLoggerProvider`. Följande kod visar en exempel kontroll klass som använder `ILogger` för att skicka loggar. Loggarna samlas in av Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Avbilda ILogger-loggar från Startup.cs och Program.cs i ASP.NET Core appar

> [!NOTE]
> I ASP.NET Core 3,0 och senare går det inte längre att mata in `ILogger` i Startup.cs och Program.cs. Se https://github.com/aspnet/Announcements/issues/353 för mer information.

Den nya ApplicationInsightsLoggerProvider kan samla in loggar från tidiga i program starts pipelinen. Även om ApplicationInsightsLoggerProvider aktive ras automatiskt i Application Insights (från och med version 2.7.1) har den inte någon Instrumentation-nyckel konfigurerad till senare i pipelinen. Därför kommer endast loggar från **Controller**/other-klasser att samlas in. Om du vill avbilda varje logg som börjar med **program.cs** och **startup.cs** , måste du uttryckligen aktivera en Instrumentation-nyckel för ApplicationInsightsLoggerProvider. Dessutom är *TelemetryConfiguration* inte helt konfigurerat när du loggar från **program.cs** eller **startup.cs** . Dessa loggar har en minimal konfiguration som använder InMemoryChannel, ingen sampling och inga standard-initierare eller-processorer för telemetri.

I följande exempel demonstreras den här funktionen med **program.cs** och **startup.cs**.

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrera från den gamla ApplicationInsightsLoggerProvider

Microsoft. ApplicationInsights. ASPNET SDK-versioner innan 2.7.1 stödde en Logging-provider som nu är föråldrad. Den här providern har Aktiver ATS via **AddApplicationInsights ()-** tilläggs metoden för ILoggerFactory. Vi rekommenderar att du migrerar till den nya providern, vilket innebär två steg:

1. Ta bort anropet *ILoggerFactory. AddApplicationInsights ()* från metoden **startup. Configure ()** för att undvika dubbel loggning.
2. Tillämpa eventuella filtrerings regler i koden eftersom de inte kommer att respekteras av den nya providern. Överlagringar av *ILoggerFactory. AddApplicationInsights ()* tog minsta LogLevel-eller filter funktioner. Med den nya providern är filtrering en del av själva loggnings ramverket. Den är inte gjord av Application Insights leverantören. Därför bör alla filter som anges via *ILoggerFactory. AddApplicationInsights ()* tas bort. Och filtrerings regler bör tillhandahållas genom att följa anvisningarna för [kontroll loggnings nivå](#control-logging-level) . Om du använder *appSettings. JSON* för att filtrera loggningen fortsätter den att fungera med den nya providern, eftersom båda använder samma provider-alias, *ApplicationInsights*.

Du kan fortfarande använda den gamla providern. (Den tas bara bort i en större versions ändring till 3. *XX*.) men vi rekommenderar att du migrerar till den nya providern av följande anledningar:

- Den tidigare providern saknar stöd för [logg omfattningar](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). I den nya providern läggs egenskaper från definitions området automatiskt till som anpassade egenskaper till den insamlade Telemetrin.
- Loggar kan nu fångas mycket tidigare i program start pipelinen. Loggar från **program** -och **Start** klasserna kan nu fångas.
- Med den nya providern görs filtrering på själva Ramverks nivån. Du kan filtrera loggar till Application Insights-providern på samma sätt som för andra leverantörer, inklusive inbyggda providers, t. ex. konsol, fel sökning och så vidare. Du kan också använda samma filter för flera providers.
- I ASP.NET Core (2,0 och senare) är det rekommenderade sättet att [Aktivera loggnings leverantörer](https://github.com/aspnet/Announcements/issues/255) att använda tilläggs metoder på ILoggingBuilder i **program.cs** .

> [!Note]
> Den nya providern är tillgänglig för program som är riktade till netstandard 2.0 eller senare. Om programmet är inriktat på äldre .NET Core-versioner, till exempel .NET core 1,1 eller om den är riktad mot .NET Framework, fortsätter du att använda den gamla providern.

## <a name="console-application"></a>Konsol program

> [!NOTE]
> Det finns ett nytt Application Insights SDK som heter [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) som kan användas för att aktivera Application Insights (ILogger och annan Application Insights telemetri) för alla konsol program. Vi rekommenderar att du använder det här paketet och tillhör ande instruktioner [härifrån.](../../azure-monitor/app/worker-service.md)

Följande kod visar ett exempel på ett konsol program som har kon figurer ATS för att skicka ILogger-spår till Application Insights.

Paket installerade:

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

I det här exemplet används det fristående paketet `Microsoft.Extensions.Logging.ApplicationInsights`. Som standard använder den här konfigurationen den "minimala" TelemetryConfiguration för att skicka data till Application Insights. Minimalt innebär att InMemoryChannel är den kanal som används. Det finns ingen sampling och ingen standard TelemetryInitializers. Det här beteendet kan åsidosättas för ett konsol program, vilket visas i följande exempel.

Installera det här ytterligare paketet:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

I följande avsnitt visas hur du åsidosätter standard-TelemetryConfiguration med hjälp av **tjänsterna. Konfigurera\<TelemetryConfiguration > ()-** metoden. Det här exemplet ställer in `ServerTelemetryChannel` och sampling. Den lägger till en anpassad ITelemetryInitializer i TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Kontrol lera loggnings nivå

ASP.NET Core *ILogger* -IR har en inbyggd mekanism för att tillämpa [logg filtrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). På så sätt kan du styra de loggar som skickas till varje registrerad Provider, inklusive Application Insights leverantören. Filtreringen kan göras antingen i konfigurationen (vanligt vis med hjälp av en *appSettings. JSON* -fil) eller i kod. Den här funktionen tillhandahålls av själva ramverket. Det är inte bara för Application Insights leverantören.

I följande exempel används filter regler till ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Skapa filter regler i konfigurationen med appSettings. JSON

För ApplicationInsightsLoggerProvider är providerns alias `ApplicationInsights`. I följande avsnitt i *appSettings. JSON* konfigureras loggar för *Varning* och över från alla kategorier och *fel* och över från kategorier som börjar med "Microsoft" att skickas till `ApplicationInsightsLoggerProvider`.

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

### <a name="create-filter-rules-in-code"></a>Skapa filter regler i kod

Följande kodfragment konfigurerar loggar för *Varning* och över från alla kategorier och för *fel* och över från kategorier som börjar med "Microsoft" som ska skickas till `ApplicationInsightsLoggerProvider`. Den här konfigurationen är samma som i föregående avsnitt i *appSettings. JSON*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Vilka är de gamla och nya versionerna av ApplicationInsightsLoggerProvider?

[Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) innehöll en inbyggd ApplicationInsightsLoggerProvider (Microsoft. ApplicationInsights. AspNetCore. logging. ApplicationInsightsLoggerProvider), som har Aktiver ATS genom **ILoggerFactory** tilläggs metoder. Den här providern har marker ATS som föråldrad från version 2.7.1. Den tas bort helt i nästa större versions ändring. 2\.6.1-paketet [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) är inte föråldrat. Det krävs för att aktivera övervakning av begär Anden, beroenden och så vidare.

Det föreslagna alternativet är det nya fristående paketet [Microsoft. Extensions. logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), som innehåller förbättrade ApplicationInsightsLoggerProvider (Microsoft. Extensions. logging. ApplicationInsights. ApplicationInsightsLoggerProvider) och tilläggs metoder på ILoggerBuilder för att aktivera det.

[Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 tar ett beroende på det nya paketet och aktiverar ILogger-avbildning automatiskt.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Varför visas vissa ILogger-loggar två gånger i Application Insights?

Duplicering kan uppstå om du har den äldre (nu föråldrade) versionen av ApplicationInsightsLoggerProvider aktive rad genom att anropa `AddApplicationInsights` på `ILoggerFactory`. Kontrol lera att **konfigurations** metoden har följande och ta bort den:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Om du får dubbel loggning när du felsöker från Visual Studio anger du `EnableDebugLogger` till *falskt* i koden som aktiverar Application Insights enligt följande. Den här dupliceringen och korrigeringen är bara relevant när du felsöker programmet.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Jag har uppdaterat till [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 och loggar från ILogger samlas in automatiskt. Vill du Hur gör jag för att inaktivera funktionen helt?

Se avsnittet [kontrol lera loggnings nivå](../../azure-monitor/app/ilogger.md#control-logging-level) för att se hur du filtrerar loggar i allmänhet. Om du vill inaktivera ApplicationInsightsLoggerProvider använder du `LogLevel.None`:

**I kod:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**I konfiguration:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Varför har vissa ILogger-loggar inte samma egenskaper som andra?

Application Insights fångar in och skickar ILogger-loggar med samma TelemetryConfiguration som används för alla andra telemetri. Men det finns ett undantag. Som standard är TelemetryConfiguration inte helt konfigurerat när du loggar från **program.cs** eller **startup.cs**. Loggar från de här platserna har inte standard konfigurationen, så de kör inte alla TelemetryInitializers och TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Jag använder det fristående paketet Microsoft. Extensions. logging. ApplicationInsights och jag vill logga ytterligare anpassad telemetri manuellt. Hur gör jag?

När du använder det fristående paketet matas `TelemetryClient` inte in i DI-behållaren, så du måste skapa en ny instans av `TelemetryClient` och använda samma konfiguration som loggarna använder, vilket visas i följande kod. Detta säkerställer att samma konfiguration används för all anpassad telemetri och telemetri från ILogger.

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
> Om du använder Microsoft. ApplicationInsights. AspNetCore-paketet för att aktivera Application Insights ändrar du koden för att få `TelemetryClient` direkt i konstruktorn. Ett exempel finns i [vanliga frågor och svar](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Vilken Application Insights telemetri skapas från ILogger-loggar? Eller var kan jag se ILogger-loggar i Application Insights?

ApplicationInsightsLoggerProvider fångar ILogger-loggar och skapar TraceTelemetry från dem. Om ett undantags objekt skickas till **log ()** -metoden på ILogger skapas *ExceptionTelemetry* i stället för TraceTelemetry. Dessa telemetri-objekt finns på samma platser som andra TraceTelemetry eller ExceptionTelemetry för Application Insights, inklusive Portal, analys eller Visual Studio Local-felsökning.

Om du föredrar att alltid skicka TraceTelemetry använder du det här kodfragmentet: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Jag har inte installerat SDK och jag använder tillägget Azure Web Apps för att aktivera Application Insights för mina ASP.NET Core-program. Hur gör jag för att använder du den nya providern? 

Application Insights tillägget i Azure Web Apps använder den nya providern. Du kan ändra filtrerings reglerna i filen *appSettings. JSON* för ditt program.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Jag använder det fristående paketet Microsoft. Extensions. logging. ApplicationInsights och aktiverar Application Insights provider genom att anropa **Builder. AddApplicationInsights ("iKey")** . Finns det något alternativ för att hämta en Instrumentation-nyckel från konfigurationen?


Ändra Program.cs och appSettings. JSON enligt följande:

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

Den här koden krävs bara när du använder en fristående Logging-Provider. Vid regelbunden Application Insights övervakning läses instrument knappen in automatiskt från konfigurations Sök vägen *ApplicationInsights: Instrumentationkey*. AppSettings. JSON bör se ut så här:

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
* [.NET-spårnings loggar i Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
