---
title: Självstudiekurs för att använda funktionsflaggor i en .NET Core-app | Microsoft-dokument
description: I den här självstudien får du lära dig hur du implementerar funktionsflaggor i .NET Core-appar.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 3182961f928a9befc5a55fb6d58e22c74ba81089
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79473446"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Självstudiekurs: Använda funktionsflaggor i en ASP.NET Core-app

.NET Core Feature Management-biblioteken ger idiomatiskt stöd för att implementera funktionsflaggor i ett .NET- eller ASP.NET Core-program. Med de här biblioteken `if` kan du deklarativt lägga till funktionsflaggor i koden så att du inte behöver skriva alla satser för dem manuellt.

Funktionshanteringsbiblioteken hanterar också funktionsflagglivscykler bakom kulisserna. Biblioteken uppdaterar och cacheföreskar tillstånd, eller garanterar att ett flaggtillstånd inte kan ändras under ett begärandeanrop. Dessutom erbjuder ASP.NET Core-biblioteket färdiga integreringar, inklusive MVC-styrenhetsåtgärder, vyer, rutter och mellanprogram.

[Tilläggsfunktionsflaggorna i en snabbstart för core-appen ASP.NET](./quickstart-feature-flag-aspnet-core.md) visar flera sätt att lägga till funktionsflaggor i ett ASP.NET Core-program. Den här självstudien förklarar dessa metoder mer i detalj. En fullständig referens finns i [dokumentationen ASP.NET Core-funktionens hantering](https://go.microsoft.com/fwlink/?linkid=2091410).

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Lägg till funktionsflaggor i viktiga delar av programmet för att styra funktionens tillgänglighet.
> * Integrera med appkonfiguration när du använder den för att hantera funktionsflaggor.

## <a name="set-up-feature-management"></a>Konfigurera funktionshantering

Lägg till en `Microsoft.FeatureManagement` referens till NuGet-paketet för att använda funktionshanteraren för .NET Core.
    
Funktionshanteraren `IFeatureManager` för .NET Core hämtar funktionsflaggor från ramverkets inbyggda konfigurationssystem. Därför kan du definiera programmets funktionsflaggor med hjälp av valfri konfigurationskälla som .NET Core stöder, inklusive de lokala *appsettings.json-fil-* eller miljövariablerna. `IFeatureManager`beroendeinjektionen .NET Core. Du kan registrera funktionshanteringstjänsterna med hjälp av standardkonventioner:

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

Som standard hämtar funktionshanteraren funktionsflaggor från `"FeatureManagement"` avsnittet i .NET Core-konfigurationsdata. I följande exempel uppmanas funktionshanteraren att `"MyFeatureFlags"` läsa från ett annat avsnitt som heter i stället:

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

Om du använder filter i funktionsflaggorna måste du inkludera ytterligare ett bibliotek och registrera det. I följande exempel visas hur du använder `PercentageFilter`ett inbyggt funktionsfilter som kallas:

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

Vi rekommenderar att du håller funktionsflaggor utanför programmet och hanterar dem separat. På så sätt kan du ändra flaggtillstånd när som helst och få dessa ändringar att träda i kraft i programmet direkt. Appkonfiguration är en centraliserad plats för att organisera och styra alla dina funktionsflaggor via ett dedikerat portalgränssnitt. Appkonfigurationen levererar också flaggorna till ditt program direkt via dess .NET Core-klientbibliotek.

Det enklaste sättet att ansluta ditt ASP.NET Core-programmet `Microsoft.Azure.AppConfiguration.AspNetCore`till App Konfiguration är via konfigurationsleverantören . Följ dessa steg för att använda detta NuGet-paket.

1. Öppna *Program.cs* filen och lägg till följande kod.

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

2. Öppna *Startup.cs* och uppdatera `Configure` metoden för att lägga till ett mellanprogram så att funktionsflaggvärdena kan uppdateras med ett återkommande intervall medan ASP.NET Core-webbappen fortsätter att ta emot begäranden.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Funktionsflaggvärden förväntas ändras med tiden. Som standard cachelagras funktionsflaggvärdena under en period av 30 sekunder, så en uppdateringsåtgärd som utlöses när mellanprogrammet tar emot en begäran uppdateras inte värdet förrän det cachelagrade värdet upphör att gälla. Följande kod visar hur du ändrar cachens förfallotid `options.UseFeatureFlags()` eller avsökningsintervall till 5 minuter i anropet.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Flagga deklaration för funktion

Varje funktionsflagga har två delar: ett namn och en lista med ett *on* eller flera filter som används `True`för att utvärdera om en funktions tillstånd är aktiverat (det vill säga när dess värde är ). Ett filter definierar ett användningsfall för när en funktion ska aktiveras.

När en funktionsflagga har flera filter genomdäms filterlistan i ordning tills ett av filtren bestämmer att funktionen ska aktiveras. Då är funktionsflaggan *aktiverad*och eventuella återstående filterresultat hoppas över. Om inget filter anger att funktionen ska aktiveras är funktionsflaggan *inaktiverad*.

Funktionshanteraren stöder *appsettings.json* som en konfigurationskälla för funktionsflaggor. I följande exempel visas hur du ställer in funktionsflaggor i en JSON-fil:

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

Av konvention `FeatureManagement` används avsnittet i det här JSON-dokumentet för funktionsflagginställningar. I föregående exempel visas tre funktionsflaggor med filter definierade i egenskapen: `EnabledFor`

* `FeatureA`är *på*.
* `FeatureB`är *avstängd*.
* `FeatureC`anger ett filter `Percentage` som `Parameters` namnges med en egenskap. `Percentage`är ett konfigurerbart filter. I det `Percentage` här exemplet anger en 50-procentig sannolikhet för att `FeatureC` flaggan ska vara *på*.

## <a name="feature-flag-references"></a>Funktionsflagga referenser

Så att du enkelt kan referera till funktionsflaggor i kod bör du definiera dem som `enum` variabler:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Kontroller av funktionsflagga

Det grundläggande mönstret för funktionshantering är att först kontrollera om en funktionsflagga är inställd *på*. Om så är fallet kör funktionshanteraren sedan de åtgärder som funktionen innehåller. Ett exempel:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Beroendeinmatning

I ASP.NET Core MVC kan du komma `IFeatureManager` åt funktionshanteraren genom beroendeinjektion:

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

## <a name="controller-actions"></a>Åtgärder för kontrollant

I MVC-styrenheter använder `FeatureGate` du attributet för att styra om en hel styrenhetsklass eller en viss åtgärd är aktiverad. Följande `HomeController` styrenhet `FeatureA` måste vara *på* innan någon åtgärd som controller-klassen innehåller kan utföras:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Följande `Index` åtgärd `FeatureA` måste vara *aktiverad* innan den kan köras:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

När en MVC-styrenhet eller åtgärd blockeras eftersom `IDisabledFeaturesHandler` kontrollfunktionsflaggan är *inaktiverad*anropas ett registrerat gränssnitt. Standardgränssnittet `IDisabledFeaturesHandler` returnerar en 404-statuskod till klienten utan svarstext.

## <a name="mvc-views"></a>MVC-vyer

I MVC-vyer kan `<feature>` du använda en tagg för att återge innehåll baserat på om en funktionsflagga är aktiverad:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Om du vill visa alternativt innehåll `negate` när kraven inte uppfylls kan attributet användas.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Funktionstaggen `<feature>` kan också användas för att visa innehåll om några eller alla funktioner i en lista är aktiverade.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-filter

Du kan ställa in MVC-filter så att de aktiveras baserat på tillståndet för en funktionsflagga. Följande kod lägger till ett `SomeMvcFilter`MVC-filter med namnet . Det här filtret utlöses endast i `FeatureA` MVC-pipelinen om det är aktiverat. Den här funktionen `IAsyncActionFilter`är begränsad till . 

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

## <a name="middleware"></a>Middleware

Du kan också använda funktionsflaggor för att villkorligt lägga till programgrenar och mellanprogram. Följande kod infogar en middleware-komponent i `FeatureA` rörledningen för begäran endast när den är aktiverad:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Den här koden bygger på den mer allmänna möjligheten att förgrena hela programmet baserat på en funktionsflagga:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du implementerar `Microsoft.FeatureManagement` funktionsflaggor i ditt ASP.NET Core-program med hjälp av biblioteken. Mer information om stöd för funktionshantering i ASP.NET Core- och Appkonfiguration finns i följande resurser:

* [ASP.NET exempelkod för kärnfunktionsflagga](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Dokumentation för Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Hantera funktionsflaggor](./manage-feature-flags.md)
