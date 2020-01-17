---
title: Självstudie för att använda funktions flaggor i en .NET Core-app | Microsoft Docs
description: I den här självstudien får du lära dig hur du implementerar funktions flaggor i .NET Core-appar.
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
ms.openlocfilehash: 4fe49c25ad71c48103f044915d187099b75b3d04
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121258"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Självstudie: använda funktions flaggor i en ASP.NET Core app

Hanterings biblioteken för .NET Core-funktioner ger idiomatiskt stöd för att implementera funktions flaggor i ett .NET-eller ASP.NET Core-program. Med de här biblioteken kan du lägga till funktions flaggor i koden så att du inte behöver skriva alla `if`-instruktioner för dem manuellt.

Biblioteken för funktions hantering hanterar även livscykler i funktions flaggor i bakgrunden. Till exempel, biblioteks uppdaterings-och cache-flaggan eller garanterar att ett flagg tillstånd kan inte ändras under ett begär ande anrop. Dessutom erbjuder ASP.NET Core biblioteket färdiga integreringar, inklusive åtgärder för MVC-kontrollant, vyer, vägar och mellanprogram.

Med [funktionen Lägg till funktions flaggor till en ASP.net Core app-snabb start](./quickstart-feature-flag-aspnet-core.md) visas flera sätt att lägga till funktions flaggor i ett ASP.net Core program. I den här självstudien beskrivs dessa metoder i detalj. En fullständig referens finns i dokumentationen för [ASP.net Core Feature Management](https://go.microsoft.com/fwlink/?linkid=2091410).

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Lägg till funktions flaggor i huvud delar av ditt program för att kontrol lera funktions tillgänglighet.
> * Integrera med app-konfiguration när du använder den för att hantera funktions flaggor.

## <a name="set-up-feature-management"></a>Konfigurera funktions hantering

.NET Core Feature Manager `IFeatureManager` hämtar funktions flaggor från ramverkets inbyggda konfigurations system. Därför kan du definiera programmets funktions flaggor genom att använda en konfigurations källa som .NET Core stöder, inklusive den lokala *appSettings. JSON* -filen eller miljövariablerna. `IFeatureManager` är beroende av .NET Core-beroende inmatning. Du kan registrera funktions hanterings tjänsterna genom att använda standard konventioner:

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

Som standard hämtar funktions hanteraren funktions flaggor från `"FeatureManagement"` avsnittet i konfigurations data för .NET Core. I följande exempel visas att funktions hanteraren kan läsa från ett annat avsnitt som kallas `"MyFeatureFlags"` i stället:

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

Om du använder filter i dina funktions flaggor måste du inkludera ett ytterligare bibliotek och registrera det. I följande exempel visas hur du använder ett inbyggt funktions filter som heter `PercentageFilter`:

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

Vi rekommenderar att du behåller funktions flaggor utanför programmet och hanterar dem separat. Om du gör det kan du när som helst ändra flaggans tillstånd och ändringarna börjar gälla i programmet direkt. Med app Configuration får du en central plats för att ordna och kontrol lera alla funktions flaggor via ett dedikerat Portal gränssnitt. App-konfigurationen skickar också flaggorna till ditt program direkt via dess .NET Core-klient bibliotek.

Det enklaste sättet att ansluta ASP.NET Core program till app-konfigurationen är via konfigurationsprovidern `Microsoft.Azure.AppConfiguration.AspNetCore`. Följ dessa steg om du vill använda det här NuGet-paketet.

1. Öppna *program.cs* -filen och Lägg till följande kod.

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

2. Öppna *startup.cs* och uppdatera `Configure`-metoden för att lägga till ett mellanprogram så att funktions flagg värden kan uppdateras vid ett återkommande intervall medan ASP.net Core webbappen fortsätter att ta emot begär Anden.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Funktions flagg värden förväntas ändras med tiden. Som standard cachelagras funktions flagg värden under en period på 30 sekunder, så en uppdaterings åtgärd utlöses när mellanprogram tar emot en begäran att inte uppdatera värdet förrän det cachelagrade värdet upphör att gälla. Följande kod visar hur du ändrar förfallo tiden för cachen eller avsöknings intervallet till 5 minuter i `options.UseFeatureFlags()`-anropet.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklaration för funktions flagga

Varje funktions flagga har två delar: ett namn och en lista med ett eller flera filter som används för att utvärdera om en funktions status är *aktiv* (det vill säga när värdet är `True`). Ett filter definierar ett användnings fall för när en funktion ska aktive ras.

När en funktions flagga har flera filter, så genomsöks filter listan i ordning tills ett av filtren bestämmer att funktionen ska aktive ras. Nu är funktions flaggan *på*och eventuella kvarvarande filter resultat hoppas över. Om inget filter anger att funktionen ska aktive ras, är funktions flaggan *avstängd*.

Funktions hanteraren stöder *appSettings. JSON* som en konfigurations källa för funktions flaggor. I följande exempel visas hur du ställer in funktions flaggor i en JSON-fil:

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

Per konvention används `FeatureManagement` avsnittet i det här JSON-dokumentet för inställningar för funktions flagga. I föregående exempel visas tre funktions flaggor med de filter som definierats i egenskapen `EnabledFor`:

* `FeatureA` är *på*.
* `FeatureB` är *avstängd*.
* `FeatureC` anger ett filter med namnet `Percentage` med en `Parameters`-egenskap. `Percentage` är ett konfigurerbart filter. I det här exemplet anger `Percentage` en sannolikhet på 50 procent för att `FeatureC` flagga ska vara *på*.

## <a name="feature-flag-references"></a>Funktions flagga referenser

Du kan enkelt referera till funktions flaggor i kod genom att definiera dem som `enum` variabler:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Funktions flagga kontrollerar

Det grundläggande mönstret för funktions hantering är att först kontrol lera om en funktions flagga har angetts till *på*. I så fall kör funktions hanteraren de åtgärder som funktionen innehåller. Ett exempel:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Beroendeinmatning

I ASP.NET Core MVC kan du komma åt funktions hanteraren `IFeatureManager` genom beroende inmatning:

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

## <a name="controller-actions"></a>Åtgärder för styrenhet

I MVC-styrenheter använder du attributet `FeatureGate` för att kontrol lera om en hel kontroll enhets klass eller en speciell åtgärd är aktive rad. Följande `HomeController` kontrollant kräver att `FeatureA` måste vara *aktiverat* innan någon åtgärd kan utföras av klassen kontrollant:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Följande `Index`s åtgärd kräver *`FeatureA` innan den* kan köras:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

När en MVC-styrenhet eller-åtgärd blockeras eftersom kontroll funktions flaggan är *inaktive rad*, anropas ett registrerat `IDisabledFeaturesHandler`-gränssnitt. Standard `IDisabledFeaturesHandler`-gränssnittet returnerar en 404-statuskod till klienten utan svars text.

## <a name="mvc-views"></a>MVC-vyer

I MVC-vyer kan du använda en `<feature>`-tagg för att återge innehåll baserat på om en funktions flagga är aktive rad:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Om du vill visa alternativt innehåll när kraven inte uppfylls kan du använda `negate` attributet.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Funktions `<feature>`s tag gen kan också användas för att visa innehåll om någon eller alla funktioner i en lista är aktiverade.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-filter

Du kan konfigurera MVC-filter så att de aktive ras baserat på status för en funktions flagga. Följande kod lägger till ett MVC-filter med namnet `SomeMvcFilter`. Det här filtret utlöses endast i MVC-pipeline om `FeatureA` har Aktiver ATS. Den här funktionen är begränsad till `IAsyncActionFilter`. 

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

## <a name="middleware"></a>Mellanprogram

Du kan också använda funktions flaggor för att villkorligt lägga till program grenar och mellanprogram. Följande kod infogar en mellanprogram komponent i pipelinen för begär Anden endast när `FeatureA` är aktive rad:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Den här koden skapar en mer allmän funktion för att förgrena hela programmet baserat på en funktions flagga:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du implementerar funktions flaggor i ditt ASP.NET Core program med hjälp av `Microsoft.FeatureManagement`-biblioteken. Mer information om stöd för funktions hantering i ASP.NET Core-och app-konfiguration finns i följande resurser:

* [Exempel kod för ASP.NET Core funktions flagga](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Dokumentation om Microsoft. FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Hantera funktionsflaggor](./manage-feature-flags.md)
