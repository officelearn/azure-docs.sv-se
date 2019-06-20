---
title: Självstudie för att använda funktionen flaggor i en .NET Core-app | Microsoft Docs
description: Lär dig hur du implementerar flaggor för funktionen i .NET Core-appar i de här självstudierna.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 5e27c6a1ab5fc9dff779c6e5d04689683d5c8e6d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274144"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Självstudier: Använd funktionen flaggor i en ASP.NET Core-app

Hanteringsbibliotek för .NET Core funktionen ger idiomatiskt support för att implementera funktionen flaggor i en .NET- eller ASP.NET Core-program. Dessa bibliotek kan du deklarativt lägga till funktionen flaggor i koden så att du inte behöver skriva alla de `if` -uttryck för dem manuellt.

Funktionen hanteringsbibliotek också hantera funktionen flaggan livscykler i bakgrunden. Till exempel biblioteken uppdatera och cachelagra flaggan tillstånd, eller garanterar ett flaggan tillstånd som inte kan ändras under ett begärandeanrop. ASP.NET Core-biblioteket erbjuder dessutom out-of the box-integreringar, inklusive MVC controller åtgärder, vyer, vägar och mellanprogram.

Den [lägga till funktionen flaggor i en ASP.NET Core-app Snabbstart](./quickstart-feature-flag-aspnet-core.md) visar flera olika sätt att lägga till funktionen flaggor i ett ASP.NET Core-program. Den här självstudien beskrivs dessa metoder i detalj. En fullständig referens finns i den [management-dokumentation för ASP.NET Core-funktionen](https://go.microsoft.com/fwlink/?linkid=2091410).

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Lägg till funktionen flaggor i viktiga delar i ditt program för att styra tillgängliga funktioner.
> * Integrera med konfiguration när du använder den för att hantera flaggor för funktionen.

## <a name="set-up-feature-management"></a>Konfigurera hantering av funktionen

Hanteraren för .NET Core-funktionen `IFeatureManager` hämtar flaggor för funktionen från Ramverkets interna konfigurationssystemet. Därför kan du kan definiera ditt programs flaggor för funktionen med hjälp av valfri konfigurationskälla som stöd för .NET Core, inklusive lokalt *appsettings.json* fil eller miljö variabler. `IFeatureManager` bygger på .NET Core beroendeinmatning. Du kan registrera hanteringstjänster funktionen med hjälp av standard konventioner:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Som standard funktionen manager hämtar flaggor för funktionen från den `"FeatureManagement"` avsnittet av data för .NET Core. I följande exempel visar funktionen manager att läsa från ett annat avsnitt som heter `"MyFeatureFlags"` i stället:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Om du använder filter i funktionen flaggor, måste du inkludera ett ytterligare bibliotek och registrera den. I följande exempel visas hur du använder ett filter för inbyggd funktion som kallas `PercentageFilter`:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Vi rekommenderar att du hålla funktionen flaggor utanför programmet och hantera dem separat. Då kan du ändra flaggan tillstånd när som helst så ändringarna träder i kraft i programmet direkt. Konfiguration av ger en central plats för att ordna och styra alla funktionen flaggor via en dedikerad portalens användargränssnitt. Konfiguration av ger också flaggor för ditt program direkt via .NET Core klienten bibliotek.

Det enklaste sättet att ansluta din ASP.NET Core-program till App-konfigurationen är via konfigurationsprovidern `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Om du vill använda den här NuGet-paketet, lägger du till följande kod till den *Program.cs* fil:

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

Funktionen flaggvärden förväntas ändras med tiden. Som standard uppdaterar funktionen manager funktionen flaggvärden med 30 sekunders mellanrum. Följande kod visar hur du kan ändra avsökningsintervallet till 5 sekunder i det `options.UseFeatureFlags()` anropa:

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(300);
           });
});
```

## <a name="feature-flag-declaration"></a>Funktionen flaggan deklaration

Varje funktionsflagga består av två delar: ett namn och en lista över en eller flera filter som används för att utvärdera om tillståndet för en funktion är *på* (det vill säga när dess värde är `True`). Ett filter definierar ett användningsfall för när en funktion ska aktiveras.

När en funktionsflagga har flera filter kan är filtrera listan slut i ordning tills ett av filtren avgör funktionen ska aktiveras. I det här läget funktionsflagga är *på*, och alla återstående filterresultat hoppas över. Om inget filter anger att funktionen ska vara aktiverad, funktionsflagga är *av*.

Funktionen manager stöder *appsettings.json* som konfigurationskälla för funktionen flaggor. I följande exempel visas hur du ställer in funktionen flaggor i en JSON-fil:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Enligt konventionen används det `FeatureManagement` används i det här JSON-dokumentet för flaggan funktionsinställningar. I föregående exempel visar tre flaggor för funktionen med sina filter som definieras i den `EnabledFor` egenskapen:

* `FeatureA` är *på*.
* `FeatureB` är *av*.
* `FeatureC` Anger ett filter med namnet `Percentage` med en `Parameters` egenskapen. `Percentage` är ett konfigurerbart filter. I det här exemplet `Percentage` anger en 50% sannolikhet för den `FeatureC` flagga för att vara *på*.

## <a name="feature-flag-references"></a>Funktionen flaggan referenser

Så att du enkelt kan referera till funktionen flaggor i kod, bör du definiera dem som `enum` variabler:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Funktionen flaggan kontroller

Det grundläggande mönstret för hantering av funktionen är att först kontrollera om en funktionsflagga är inställd på *på*. Om därför funktionen manager sedan kör åtgärderna som innehåller funktionen. Exempel:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Beroendeinmatning

I ASP.NET Core MVC, kan du komma åt funktionen manager `IFeatureManager` via beroendeinmatning:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Åtgärder för domänkontrollant

I MVC-kontrollanter, använder du den `FeatureGate` attributet kontroll om hela kontrollantklassen eller en specifik åtgärd är aktiverat. Följande `HomeController` kontrollenheten kräver `FeatureA` vara *på* innan något kontrollantklassen innehåller kan köras:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Följande `Index` -åtgärden kräver `FeatureA` vara *på* innan du kan använda:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

När en MVC-enhet eller en åtgärd är blockerad eftersom styra funktionsflagga är *av*, en registrerad `IDisabledFeaturesHandler` gränssnitt kallas. Standard `IDisabledFeaturesHandler` gränssnittet returnerar ett 404-statuskod till klienten utan någon svarstext.

## <a name="mvc-views"></a>MVC-vyer

I MVC-vyer, kan du använda en `<feature>` taggen för att återge innehåll baserat på huruvida en funktionsflagga är aktiverat:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Att visa alternativa innehåll om kraven inte uppfylls på `negate` attributet kan användas.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Funktionen `<feature>` tagg kan också användas för att visa innehåll om några eller alla funktioner i en lista som är aktiverade.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-filter

Du kan ställa in MVC filter så att de visas baserat på status för en funktionsflagga. Följande kod lägger till ett MVC-filter med namnet `SomeMvcFilter`. Det här filtret utlöses i MVC pipeline endast om `FeatureA` är aktiverad.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="routes"></a>Vägar

Du kan använda funktionen flaggor för att dynamiskt exponera vägar. Följande kod lägger till en väg som anger `Beta` som standard-domänkontrollant endast när `FeatureA` är aktiverat:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

Du kan också använda funktionen flaggor för att villkorligt lägga till programmet grenar och mellanprogram. Följande kod infogningar som en mellanprogram komponent i begäran pipeline endast när `FeatureA` är aktiverat:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Den här koden skapar av mer allmän möjlighet att grenen hela programmet baserat på en funktionsflagga:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs hur du implementerar funktionen flaggor i ASP.NET Core-program med hjälp av den `Microsoft.FeatureManagement` bibliotek. Mer information om support för funktionen i ASP.NET Core och konfiguration finns i följande resurser:

* [ASP.NET Core funktionsflagga exempelkoden](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Hantera funktionsflaggor](./manage-feature-flags.md)
