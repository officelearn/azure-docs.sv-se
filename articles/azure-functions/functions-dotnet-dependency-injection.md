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
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: e31f3dc166177ce36289b97d85d90a9582c9cae5
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375985"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Använd beroende inmatning i .NET Azure Functions

Azure Functions stöder design mönstret för program beroende insprutning (DI), vilket är en teknik för att uppnå [inversion av kontrollen (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mellan klasser och deras beroenden.

Azure Functions versioner ovanpå ASP.NET Core beroende inmatnings funktioner. Vi rekommenderar att du är medveten om tjänster, livs längd och design mönster för [ASP.net Core beroende insprutning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) innan du använder di-funktioner i en Azure Functions app.

Stöd för beroende inmatning börjar med Azure Functions 2. x.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan använda beroende inmatning måste du installera följande NuGet-paket:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft. net. SDK. Functions-paket](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) version 1.0.28 eller senare

- Valfritt: [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) krävs endast för registrering av httpclient vid start

## <a name="register-services"></a>Registrera tjänster

För att registrera tjänster kan du skapa en metod för att konfigurera och lägga till komponenter `IFunctionsHostBuilder` i en instans.  Azure Functions-värden skapar en instans av `IFunctionsHostBuilder` och skickar den direkt till din metod.

Registrera-metoden genom att lägga till `FunctionsStartup` attributet Assembly som anger typ namnet som används vid start. Kod refererar också till en för hands version av [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) på NuGet.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Använda inmatade beroenden

ASP.NET Core använder konstruktorn för att göra dina beroende tillgängliga för din funktion. Följande exempel visar hur `IMyService` och `HttpClient` beroenden matas in i en http-utlöst funktion. 

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

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

Användningen av konstruktorn för konstruktorn innebär att du inte bör använda statiska funktioner om du vill dra nytta av beroende inmatning. Cosmos-klienten hittar [detta](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/CodeSamples/AzureFunctions/AzureFunctionsCosmosClient.cs).

## <a name="service-lifetimes"></a>Livs längd för tjänsten

Azure Functions appar ger samma tjänste livstid som ASP.NET- [beroende inmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): tillfällig, omfattning och singleton.

I en Functions-app matchar en omfattning för Omfattningens livs längd en funktions körnings livs längd. Omfångs tjänster skapas en gång per körning. Senare begär Anden för tjänsten under körningen återanvänder den befintliga tjänst instansen. En singleton-tjänstelivs längd motsvarar värdens livstid och återanvänds över funktions körningar på den instansen.

Singleton livstids tjänster rekommenderas för anslutningar och klienter, till exempel `SqlConnection` `CloudBlobClient`, eller `HttpClient` instanser.

Visa eller hämta ett [exempel på olika livs längder för tjänsten](https://aka.ms/functions/di-sample) på GitHub.

## <a name="logging-services"></a>Loggnings tjänster

Om du behöver en egen Logging-Provider är det rekommenderade sättet att registrera en `ILoggerProvider` instans. Application Insights läggs till av Azure Functions automatiskt.

> [!WARNING]
> Lägg `AddApplicationInsightsTelemetry()` inte till i samlingen tjänster när den registrerar tjänster som står i konflikt med tjänster som tillhandahålls av miljön.

## <a name="function-app-provided-services"></a>Function-appen tillhandahåller tjänster

Funktions värden registrerar många tjänster. Följande tjänster är säkra att ta med i ditt program:

|Typ av tjänst|Giltighet|Beskrivning|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Körnings konfiguration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Ansvarar för att tillhandahålla ID för värd instansen|

Om det finns andra tjänster som du vill ta ett beroende på, [skapar du ett ärende och föreslår dem på GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Åsidosätta värd tjänster

Det finns för närvarande inte stöd för att åsidosätta tjänster som tillhandahålls av värden.  Om det finns tjänster som du vill åsidosätta [skapar du ett ärende och föreslår dem på GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

- [Så här övervakar du din Function-app](functions-monitoring.md)
- [Metod tips för functions](functions-best-practices.md)
