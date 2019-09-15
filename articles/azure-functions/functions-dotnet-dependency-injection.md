---
title: Använd beroende inmatning i .NET Azure Functions
description: Lär dig hur du använder beroende inmatning för att registrera och använda tjänster i .NET Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, functions, arkitektur utan Server
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 09bcce6daf519c7d5e99c7c120064f5c8bb92475
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996878"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Använd beroende inmatning i .NET Azure Functions

Azure Functions stöder design mönstret för program beroende insprutning (DI), vilket är en teknik för att uppnå [inversion av kontrollen (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mellan klasser och deras beroenden.

- Beroende inmatning i Azure Functions bygger på funktionerna för .NET Core-beroende inmatning. Det rekommenderas att du är bekant med [.net Core-beroende inmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) . Det finns dock skillnader i hur du åsidosätter beroenden och hur konfigurations värden läses med Azure Functions i förbruknings planen.

- Stöd för beroende inmatning börjar med Azure Functions 2. x.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan använda beroende inmatning måste du installera följande NuGet-paket:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft. net. SDK. Functions-paket](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) version 1.0.28 eller senare

## <a name="register-services"></a>Registrera tjänster

Registrera tjänster genom att skapa en metod för att konfigurera och lägga till komponenter `IFunctionsHostBuilder` i en instans.  Azure Functions-värden skapar en instans av `IFunctionsHostBuilder` och skickar den direkt till din metod.

Registrera-metoden genom att lägga till `FunctionsStartup` attributet Assembly som anger typ namnet som används vid start.

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

En serie registrerings steg som körs före och efter körningen bearbetar start klassen. Tänk på följande saker:

- *Start klassen är endast avsedd för installation och registrering.* Undvik att använda tjänster som registrerats vid start under start processen. Försök till exempel inte att logga ett meddelande i en loggad logg som registreras under start. Den här tidpunkten för registrerings processen är för tidig för att tjänsterna ska kunna användas. `Configure` När metoden har körts fortsätter funktions körningen att registrera ytterligare beroenden, vilket kan påverka hur dina tjänster fungerar.

- *Den beroende injektions behållaren innehåller bara explicit registrerade typer*. De enda tjänster som är tillgängliga som inmatnings bara typer är vad `Configure` som ställts in i-metoden. Därför är funktions typerna som `BindingContext` och `ExecutionContext` inte tillgängliga under installationen eller som inmatnings bara typer.

## <a name="use-injected-dependencies"></a>Använda inmatade beroenden

Konstruktorn för konstruktorn används för att göra beroenden tillgängliga i en funktion. Användningen av konstruktorn för konstruktorn kräver att du inte använder statiska klasser.

Följande exempel visar hur `IMyService` och `HttpClient` beroenden matas in i en http-utlöst funktion. I det här exemplet används paketet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) som krävs `HttpClient` för att registrera ett vid start.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
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

## <a name="service-lifetimes"></a>Livs längd för tjänsten

Azure Functions appar ger samma tjänste livstid som ASP.NET- [beroende inmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). För en Functions-app beter sig de olika livs längderna för tjänsten enligt följande:

- **Tillfälligt**: Tillfälliga tjänster skapas vid varje begäran av tjänsten.
- **Omfattning**: Livs längden för omfattnings tjänsten matchar en livs längd för funktions körning. Omfångs tjänster skapas en gång per körning. Senare begär Anden för tjänsten under körningen återanvänder den befintliga tjänst instansen.
- **Singleton**: Singleton-tjänstens livs längd motsvarar värdens livstid och återanvänds över funktions körningar på den instansen. Singleton livstids tjänster rekommenderas för anslutningar och klienter, till exempel `SqlConnection` eller `HttpClient` instanser.

Visa eller hämta ett [exempel på olika livs längder för tjänsten](https://aka.ms/functions/di-sample) på GitHub.

## <a name="logging-services"></a>Loggnings tjänster

Om du behöver en egen Logging-Provider registrerar du en anpassad typ som `ILoggerProvider` en instans. Application Insights läggs till av Azure Functions automatiskt.

> [!WARNING]
> - Lägg `AddApplicationInsightsTelemetry()` inte till i samlingen tjänster när den registrerar tjänster som står i konflikt med tjänster som tillhandahålls av miljön.
> - Registrera inte din egen `TelemetryConfiguration` eller `TelemetryClient` om du använder de inbyggda Application Insights funktionerna.

## <a name="function-app-provided-services"></a>Function-appen tillhandahåller tjänster

Funktions värden registrerar många tjänster. Följande tjänster är säkra att ta med i ditt program:

|Typ av tjänst|Giltighet|Beskrivning|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Körnings konfiguration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Ansvarar för att tillhandahålla ID för värd instansen|

Om det finns andra tjänster som du vill ta ett beroende på, [skapar du ett ärende och föreslår dem på GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Åsidosätta värd tjänster

Det finns för närvarande inte stöd för att åsidosätta tjänster som tillhandahålls av värden.  Om det finns tjänster som du vill åsidosätta [skapar du ett ärende och föreslår dem på GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Arbeta med alternativ och inställningar

Värden som definieras i [appinställningar](./functions-how-to-use-azure-function-app-settings.md#settings) är tillgängliga i en `IConfiguration` instans, vilket gör att du kan läsa inställningarna för appens inställningar i Start klassen.

Du kan extrahera värden från `IConfiguration` instansen till en anpassad typ. Genom att kopiera inställningarna för appen till en anpassad typ gör det enkelt att testa dina tjänster genom att göra dessa värden inmatnings bara. Överväg följande klass som innehåller en egenskap som heter konsekvent med en app-inställning.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Inifrån `IConfiguration` - metodenkanduextraheravärdenfråninstansentilldinanpassadetypmedhjälp`Startup.Configure` av följande kod:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

Anropar `Bind` kopierings värden som har matchande egenskaps namn från konfigurationen till den anpassade instansen. Alternativ instansen är nu tillgänglig i IoC-behållaren för inmatning i en funktion.

Options-objektet matas in i funktionen som en instans av det generiska `IOptions` gränssnittet. `Value` Använd egenskapen för att få åtkomst till de värden som finns i konfigurationen.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _service = service;
        _settings = options.Value;
    }
}
```

Mer information om hur du arbetar med alternativ finns [i alternativ mönster i ASP.net Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) .

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

- [Så här övervakar du din Function-app](functions-monitoring.md)
- [Metod tips för functions](functions-best-practices.md)
