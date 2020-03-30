---
title: Använda beroendeinmatning i .NET Azure Functions
description: Lär dig hur du använder beroendeinjektion för att registrera och använda tjänster i .NET-funktioner
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: f8f1eb353087c5121eaafb4c8789e7a2f7638b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475129"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Använda beroendeinmatning i .NET Azure Functions

Azure Functions stöder beroendeinjektion (DI) programdesignmönster, vilket är en teknik för att uppnå [inversion av kontroll (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mellan klasser och deras beroenden.

- Beroendeinjektion i Azure Functions bygger på funktionerna för .NET Core Dependency Dependency Injection. Förtrogenhet med [beroendeinjektionen .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) rekommenderas. Det finns dock skillnader i hur du åsidosätter beroenden och hur konfigurationsvärden läss med Azure-funktioner i förbrukningsplanen.

- Stöd för beroendeinjektion börjar med Azure Functions 2.x.

## <a name="prerequisites"></a>Krav

Innan du kan använda beroendeinjektion måste du installera följande NuGet-paket:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions paketversion](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 eller senare

## <a name="register-services"></a>Registrera tjänster

Om du vill registrera tjänster skapar du `IFunctionsHostBuilder` en metod för att konfigurera och lägga till komponenter i en instans.  Azure Functions-värden skapar en `IFunctionsHostBuilder` instans av och skickar den direkt till din metod.

Om du vill registrera `FunctionsStartup` metoden lägger du till sammansättningsattributet som anger det typnamn som används vid start.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Varningar

En serie registreringssteg körs före och efter körningen bearbetar startklassen. Tänk därför på följande:

- *Startklassen är endast avsedd för installation och registrering.* Undvik att använda tjänster som registrerats vid start under startprocessen. Försök till exempel inte att logga ett meddelande i en logger som registreras under start. Denna punkt i registreringsprocessen är för tidigt för dina tjänster att vara tillgängliga för användning. När `Configure` metoden har körts fortsätter körningen Funktioner att registrera ytterligare beroenden, vilket kan påverka hur tjänsterna fungerar.

- *Behållaren för beroendeinsprutning innehåller endast explicit registrerade typer*. De enda tjänster som är tillgängliga som `Configure` injicerbara typer är vad som är inställningar i metoden. Därför är funktioner-specifika typer `BindingContext` `ExecutionContext` som och inte tillgängliga under installationen eller som injicerbara typer.

## <a name="use-injected-dependencies"></a>Använda injicerade beroenden

Konstruktorinjektion används för att göra dina beroenden tillgängliga i en funktion. Användning av konstruktorinjektion kräver att du inte använder statiska klasser.

Följande exempel visar hur `IMyService` `HttpClient` beroenden och injiceras i en HTTP-utlöst funktion. I det här exemplet används det [Microsoft.Extensions.Http-paket](https://www.nuget.org/packages/Microsoft.Extensions.Http/) som krävs för att registrera ett `HttpClient` vid start.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Livslängd för tjänsten

Azure Functions-appar ger samma livslängd som [ASP.NET beroendeinjektion](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). För en functions-app fungerar de olika tjänstlivslängderna på följande sätt:

- **Övergående**: Tillfälliga tjänster skapas på varje begäran av tjänsten.
- **Scoped**: Den begränsade tjänstens livstid matchar en livstid för funktionskörning. Begränsade tjänster skapas en gång per körning. Senare begäranden för den tjänsten under körningen återanvända den befintliga tjänstinstansen.
- **Singleton**: Singleton-tjänstens livstid matchar värdens livstid och återanvänds över funktionskörningar på den instansen. Singleton livstid tjänster rekommenderas för anslutningar och `SqlConnection` `HttpClient` klienter, till exempel eller instanser.

Visa eller hämta ett [exempel på olika tjänstlivslängder](https://aka.ms/functions/di-sample) på GitHub.

## <a name="logging-services"></a>Loggningstjänster

Om du behöver en egen loggningsleverantör `ILoggerProvider` registrerar du en anpassad typ som en instans. Application Insights läggs till av Azure Functions automatiskt.

> [!WARNING]
> - Lägg inte `AddApplicationInsightsTelemetry()` till tjänsteinsamlingen eftersom den registrerar tjänster som står i konflikt med tjänster som tillhandahålls av miljön.
> - Registrera inte din `TelemetryConfiguration` `TelemetryClient` egen eller om du använder den inbyggda Application Insights-funktionen. Om du behöver konfigurera `TelemetryClient` din egen instans kan `TelemetryConfiguration` du skapa en via det injicerade som visas i [Övervaka Azure-funktioner](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> och ILoggerFactory

Värden kommer `ILogger<T>` att `ILoggerFactory` injicera och tjänster i konstruktörer.  Som standard filtreras dock dessa nya loggningsfilter ut ur funktionsloggarna.  Du måste ändra `host.json` filen för att välja ytterligare filter och kategorier.  Följande exempel visar att `ILogger<HttpTrigger>` du lägger till en med-loggar som ska exponeras av värden.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

Och `host.json` en fil som lägger till loggfiltret.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Tjänster för funktionsapp som tillhandahålls

Funktionsvärden registrerar många tjänster. Följande tjänster är säkra att ta som ett beroende i ditt program:

|Typ av tjänst|Livstid|Beskrivning|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Konfiguration för körning|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Ansvarig för att tillhandahålla ID för värdinstansen|

Om det finns andra tjänster som du vill vara beroende av [skapar du ett problem och föreslår dem på GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Övergripande värdtjänster

Åsidosättningstjänster som tillhandahålls av värden stöds för närvarande inte.  Om det finns tjänster som du vill åsidosätta [skapar du ett problem och föreslår dem på GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Arbeta med alternativ och inställningar

Värden som definieras i [appinställningarna](./functions-how-to-use-azure-function-app-settings.md#settings) är tillgängliga i en `IConfiguration` instans, vilket gör att du kan läsa värden för appinställningar i startklassen.

Du kan extrahera `IConfiguration` värden från förekomsten till en anpassad typ. Genom att kopiera värdena för appinställningarna till en anpassad typ kan det enkelt testa dina tjänster genom att göra dessa värden injicerbara. Inställningar som läss in i konfigurationsinstansen måste vara enkla nyckel-/värdepar.

Tänk på följande klass som innehåller en egenskap som heter som överensstämmer med en appinställning:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Och `local.settings.json` en fil som kan strukturera den anpassade inställningen enligt följande:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Inifrån `Startup.Configure` metoden kan du extrahera `IConfiguration` värden från förekomsten till din anpassade typ med hjälp av följande kod:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Anropa `Bind` kopierar värden som har matchande egenskapsnamn från konfigurationen till den anpassade instansen. Alternativinstansen är nu tillgänglig i IoC-behållaren för att injicera i en funktion.

Alternativobjektet injiceras i funktionen som en `IOptions` förekomst av det allmänna gränssnittet. Använd `Value` egenskapen för att komma åt de värden som finns i konfigurationen.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Se [alternativ mönster i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) för mer information om hur du arbetar med alternativ.

> [!WARNING]
> Undvik att försöka läsa värden från filer som *local.settings.json* eller *appsettings.{ miljö}.json* i förbrukningsplanen. Värden som läs av dessa filer som är relaterade till utlösande anslutningar är inte tillgängliga eftersom appen skalas eftersom värdinfrastrukturen inte har åtkomst till konfigurationsinformationen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

- [Så här övervakar du din funktionsapp](functions-monitoring.md)
- [Metodtips för funktioner](functions-best-practices.md)
