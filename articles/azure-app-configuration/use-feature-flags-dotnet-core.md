---
title: Självstudie för att använda funktionen flaggor i en .NET Core-app | Microsoft Docs
description: I den här självstudien får du lära dig hur du implementerar flaggor för funktionen i .NET Core-appar
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
ms.openlocfilehash: 28ba4397ca5a5fd3c281555238fc7eec8a82943d
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413676"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Självstudier: Använda funktionsflaggor i en .NET Core-app

Hanteringsbibliotek för .NET Core funktionen ger idiomatiskt support för att implementera funktionen flaggor i ett .NET- eller ASP.NET Core-program. De kan du lägga till funktionen flaggar i koden mer deklarativt så att du inte behöver skriva alla de `if` -uttryck för dem manuellt. De hanterar funktionen flaggan livscykler (till exempel uppdatering och cache flaggan tillstånd, garanterar ett flaggan tillstånd som inte kan ändras under ett begärandeanrop) bakom scenen. ASP.NET Core-biblioteket erbjuder dessutom out-of the box integreringar inklusive MVC controller åtgärder, vyer, vägar och mellanprogram.

Den [lägga till funktionen flaggor i en ASP.NET Core-app](./quickstart-feature-flag-aspnet-core.md) visas ett antal sätt att lägga till funktionen flaggor i ett ASP.NET Core-program. Den här självstudien beskrivs dessa i mer information. Se den [management-dokumentation för ASP.NET Core-funktionen](https://go.microsoft.com/fwlink/?linkid=2091410) för en fullständig referens.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägg till funktionen flaggor i viktiga delar i ditt program för att styra tillgängliga funktioner.
> * Integrera med konfiguration när du använder det för att hantera flaggor för funktionen.

## <a name="setup"></a>Inställningar

Hanteraren för .NET Core-funktionen `IFeatureManager` hämtar flaggor för funktionen från Ramverkets interna konfigurationssystemet. Därför kan du definiera ditt programs funktionen flaggor med hjälp av valfri konfigurationskälla som stöd för .NET Core, inklusive lokalt *appsettings.json* fil eller miljö variabler. Funktionen manager bygger på .NET Core beroendeinmatning. Du kan registrera funktionen hanteringstjänster med konventioner som standard.

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

Funktionen manager hämtar flaggor för funktionen från avsnittet ”FeatureManagement” av data för .NET Core som standard. I följande exempel beordrar den att läsa från ett annat avsnitt som heter ”MyFeatureFlags” i stället.

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

Om du använder filter i funktionen flaggor, måste du inkludera ett ytterligare bibliotek och registrera den. I följande exempel visas hur du använder ett filter för inbyggd funktion som kallas **PercentageFilter ”**.

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

För att fungera effektivt, bör du hålla funktionen flaggor utanför programmet och hantera dem separat. Då kan du ändra flaggan tillstånd när som helst och har ändringarna träder i kraft i programmet direkt. Konfiguration av ger en central plats för att ordna och hur du styr alla funktionen flaggar via en dedikerad portalens användargränssnitt och levererar flaggor för ditt program direkt via .NET Core klienten bibliotek. Det enklaste sättet att ansluta din ASP.NET Core-program till App-konfigurationen är via konfigurationsprovidern `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Du kan använda den här NuGet-paketet i din kod genom att lägga till följande för att den *Program.cs* fil:

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

Funktionen flaggvärden förväntas ändras med tiden. Som standard uppdateras funktionen manager funktionen flaggvärden var 30: e sekund. Du kan använda en annan avsökningsintervall i den `options.UseFeatureFlags()` anrop ovan.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Funktionen flaggan deklaration

Varje funktionsflagga består av två delar: ett namn och en lista över en eller flera filter som används för att utvärdera om tillståndet för en funktion är *på* (det vill säga när dess värde är `True`). Ett filter definierar ett användningsfall som en funktion ska vara aktiverat. Om en funktionsflagga har flera filter kan är filtrera listan slut i ordning tills ett av filtren anger att funktionen ska aktiveras. Nu funktionsflagga anses *på* och eventuella återstående filterresultat hoppas över. Om inget filter anger att funktionen ska vara aktiverad, funktionsflagga är *av*.

Funktionen manager stöder *appsettings.json* som konfigurationskälla för funktionen flaggor. I följande exempel visas hur du ställer in funktionen flaggor i en json-fil.

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
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

Enligt konventionen används det `FeatureManagement` används i det här json-dokumentet för flaggan funktionsinställningar. Exemplet ovan visar tre flaggor för funktionen med sina filter som definieras i den *EnabledFor* egenskapen:

* **FeatureA** är *på*.
* **FeatureB** är *av*.
* **FeatureC** anger ett filter med namnet *procent* med en *parametrar* egenskapen. *Procentandel* är ett exempel på ett konfigurerbart filter och anger en 50% sannolikhet för den **FeatureC** flagga för att vara *på*.

## <a name="referencing"></a>Refererar till

Även om inte krävs, flaggor för funktionen ska vara definierat som `enum` variablerna så att de kan refereras enkelt i kod.

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>Kontroll av funktionen flagga

Det grundläggande mönstret för hantering av funktionen är att först kontrollera om en funktionsflagga är inställd på *på* och utför åtgärderna som är inom klammerparenteserna om så är fallet.

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Beroendeinmatning

I ASP.NET Core MVC, funktionen manager `IFeatureManager` kan nås via beroendeinmatning.

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

## <a name="controller-action"></a>Åtgärd för domänkontrollant

I MVC-kontrollanter, en `Feature` attributet kan användas till att kontrollera om hela kontrollantklassen eller en specifik åtgärd är aktiverat. Följande `HomeController` kontrollenheten kräver *FeatureA* vara *på* innan du kan köra alla åtgärder som den innehåller.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Följande `Index` åtgärden ovan kräver *FeatureA* vara *på* innan det kan köras.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

När en MVC-enhet eller en åtgärd är blockerad eftersom styra funktionsflagga är *av*, en registrerad `IDisabledFeatureHandler` anropas. Standard `IDisabledFeatureHandler` returnerar ett 404-statuskod till klienten utan någon svarstext.

## <a name="view"></a>Visa

I MVC-vyer, en `<feature>` tagg som kan användas för att återge innehåll baserat på om en funktionsflagga är aktiverat eller inte.

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>MVC-filter

MVC-filter kan ställas in så att de aktiveras baserat på status för en funktionsflagga. Detta lägger till ett MVC-filter med namnet `SomeMvcFilter`. Det här filtret utlöses i MVC pipeline endast om *FeatureA* är aktiverad.

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

## <a name="route"></a>Routa

Vägar kan exponeras dynamiskt baserat på flaggor för funktionen. Detta lägger till en väg som anger `Beta` som standard-domänkontrollant endast när *FeatureA* är aktiverad.

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

Flaggor för funktionen kan användas för att lägga till programmet grenar och mellanprogram villkorligt. Följande infogar en mellanprogram komponent i begäran pipeline endast när *FeatureA* är aktiverad.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Detta skapar av mer allmän möjlighet att grenen hela programmet baserat på en funktionsflagga.

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs hur du implementerar funktionen flaggor i ASP.NET Core-program genom att använda den `Microsoft.FeatureManagement` bibliotek. Se följande resurser för mer information på funktionsstöd för hantering i ASP.NET Core och konfiguration.

* [ASP.NET Core funktionsflagga exempelkoden]()
* [Microsoft.FeatureManagement dokumentation]()
* [Hantera funktionsflaggor](./manage-feature-flags.md)
