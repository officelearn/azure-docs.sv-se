---
title: Utforska .NET spårningsloggarna i Azure Application Insights med ILogger
description: Exempel på hur du använder Azure Application Insights ILogger-implementering med ASP.NET Core- och -konsolen.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: c456f8f7f08fdbd0020bfc49ceeec262fa0ac773
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56457981"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core stöder en API för loggning som fungerar med en mängd olika leverantörer av inbyggda och tredjeparts-loggning. Den här artikeln visar hur du hanterar loggning med Application Insights ILogger-implementering i både konsolen och ASP.NET Core-program. Läs mer om ILogger baserat loggning i [i den här artikeln](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Konsolprogram

Nedan visas ett konsolexempelprogram som konfigurerats för att skicka ILogger spårningar till Application Insights.

Paket som är installerade:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
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
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>ASP.NET Core-program

Följande visar ett urval ASP.NET Core-program som har konfigurerats för att skicka ILogger spårningar till application insights. Det här exemplet kan följas för att skicka ILogger spårningar från Program.cs, Startup.cs eller några andra Controller/programlogik.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

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
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up up by Application Insights.   
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

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
        // All the following logs will be picked upby Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

I båda exemplen ovan är det fristående paketet används Microsoft.Extensions.Logging.ApplicationInsights. Den här konfigurationen använder som standard minst `TelemetryConfiguration` för att skicka data till Application Insights. Minsta innebär att den kanal som används `InMemoryChannel`, inga provtagning och ingen standard TelemetryInitializers. Det här beteendet kan åsidosättas för ett konsolprogram som visas i exemplet nedan.

Installera den här ytterligare paket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

I följande avsnitt beskrivs hur du kan åsidosätta standardinställningen `TelemetryConfiguration`. Det här exemplet konfigurerar `ServerTelemetryChannel`, provtagning, och en anpassad `ITelemetryInitializer`.

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

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

Detta tillvägagångssätt kan användas i ett ASP.NET Core-program, är ett allt vanligare metod att kombinera regelbundna programövervakning (begäranden, beroenden osv.) med ILogger avbildning enligt nedan.

Installera den här ytterligare paket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Lägg till följande till den `ConfigureServices` metoden. Den här koden gör regelbundna programövervakning med standardkonfiguration (ServerTelemetryChannel, LiveMetrics, begäran/beroenden, korrelation osv.)

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

I det här exemplet används konfigurationen av `ApplicationInsightsLoggerProvider` är densamma som används av regelbundna programövervakning. Därför båda `ILogger` spår och annan telemetri (begäranden, beroenden osv.) körs samma uppsättning `TelemetryInitializers`, `TelemetryProcessors`, och `TelemetryChannel`. De korrelerade och samplas/inte samplas på samma sätt.

Det är dock undantag till detta beteende. Standard `TelemetryConfiguration` fullständigt konfiguration när du loggar inte något från `Program.cs` eller `Startup.cs` själva, så dessa loggar inte standardkonfigurationen. Alla andra logga (till exempel loggar från domänkontrollanter, modeller osv) skulle dock dela konfigurationen.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [ILogger baserat loggning](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [Spårningsloggar för .NET](../../azure-monitor/app/asp-net-trace-logs.md)
