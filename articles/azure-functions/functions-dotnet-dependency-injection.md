---
title: Använd beroendeinmatning i .NET Azure Functions
description: Lär dig hur du använder beroendeinmatning för att registrera och använda tjänster i .NET-funktioner
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktioner, serverlös arkitektur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408451"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Använd beroendeinmatning i .NET Azure Functions

Azure Functions har stöd för beroende inmatning (DI) programvara designmönstret, vilket är en teknik för att uppnå [invertering-kontroll (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mellan klasser och deras beroenden.

Azure Functions bygger på Beroendeinmatning för ASP.NET Core-funktioner.  Du bör känna till tjänster, livslängd och designmönster för [ASP.NET Core beroendeinmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) innan du använder dem i funktioner.

## <a name="installing-dependency-injection-packages"></a>Installera beroendepaket för inmatning

För att kunna använda beroende inmatning funktioner behöver du inkludera i NuGet-paketet som exponerar dessa API: er.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Registrera tjänster

Om du vill registrera tjänster som du kan skapa en konfigurera-metod och lägga till komponenter till en `IFunctionsHostBuilder` instans.  Värden för Azure Functions skapar en `IFunctionsHostBuilder` och skickar dem direkt i din konfigurerade metod.

Registrera din konfigurera metod, måste du lägga till ett sammansättningen-attribut som anger typen för din konfigurera metoden med hjälp av den `FunctionsStartup` attribut.

```csharp
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

## <a name="service-lifetimes"></a>Tjänstens livslängd

Azure-funktionsappar tillhandahålla samma service-livslängd som [ASP.NET Beroendeinmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes), tillfälliga, begränsade, och singleton.

I en funktionsapp matchar en livslängd för begränsade tjänsten en livslängd för körning av funktionen. Begränsade tjänster skapas en gång per körning.  Senare begäranden för tjänsten under körningen återanvända den instansen.  En singleton-tjänsten livslängd matchar värden livslängd och återanvändas i funktionskörningar på instansen.

Singleton-tjänsterna livslängd rekommenderas för anslutningar och klienter, till exempel en `SqlConnection`, `CloudBlobClient`, eller `HttpClient`.

Visa eller ladda ned en [prov på olika service livslängd](https://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Loggningstjänster

Om du behöver en egen loggningsprovider det rekommenderade sättet är att registrera en `ILoggerProvider`.  För Application Insights Functions lägger till Application Insights automatiskt åt dig.  

> [!WARNING]
> Lägg inte till `AddApplicationInsightsTelemetry()` till tjänsterna samling eftersom det registreras tjänster som är i konflikt med det tillhandahålls av miljön. 
 
## <a name="function-app-provided-services"></a>Funktionen tillhandahålls apptjänster

Funktionen värden ska registrera många tjänster själva.  Nedan finns tjänster som är säkert att skapa ett beroende på.  Andra tjänster stöds inte för att registrera eller beror på.  Om det finns andra tjänster du vill skapa ett beroende på,. [skapa ett problem och en beskrivning på GitHub](https://github.com/azure/azure-functions-host).

|Typ av tjänst|Livslängd|Beskrivning|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton-instans|Runtime-konfiguration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton-instans|Ansvarar för att tillhandahålla ID för värdinstans|

### <a name="overriding-host-services"></a>Åsidosätta tjänster

Åsidosätta tjänster som tillhandahålls av värden stöds för närvarande inte.  Om det finns tjänster som du vill åsidosätta, [skapa ett problem och en beskrivning på GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Så här övervakar du din funktionsapp](functions-monitoring.md)
* [Metodtips för functions](functions-best-practices.md)