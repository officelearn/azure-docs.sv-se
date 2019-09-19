---
title: Snabb start för att lägga till funktions flaggor i ASP.NET Core | Microsoft Docs
description: En snabb start för att lägga till funktions flaggor i ASP.NET Core appar och hantera dem i Azure App konfiguration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 6f9094a52ff3558fa8d1f2fee1d80ed8eb09a416
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076329"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Snabbstart: Lägga till funktions flaggor i en ASP.NET Core app

I den här snabb starten inkluderar du Azure App konfiguration i en ASP.NET Core webbapp för att skapa en slut punkt till slutpunkt-implementering av funktions hantering. Du kan använda app Configuration service för att centralt lagra alla funktions flaggor och kontrol lera deras tillstånd. 

Biblioteken för .NET Core Feature Management utökar ramverket med omfattande stöd för funktions flaggor. Dessa bibliotek skapas ovanpå konfigurations systemet för .NET Core. De integreras sömlöst med app-konfigurationen via sin .NET Core-Konfigurationsprovider.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **funktions hanteraren** >  **+ Lägg** till för att lägga till följande funktions flaggor:

    | Nyckel | State |
    |---|---|
    | Beta | Av |

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Du använder [.net Core kommando rads gränssnitt (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt ASP.net Core MVC-webbprogram. Fördelen med att använda .NET Core CLI i stället för Visual Studio är att .NET Core CLI är tillgängligt på Windows-, macOS-och Linux-plattformarna.

1. Skapa en ny mapp för ditt projekt. I den här snabb starten ska du ge den namnet *TestFeatureFlags*.

1. I den nya mappen kör du följande kommando för att skapa ett nytt ASP.NET Core MVC-webbprogram-projekt:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Lägga till Secret Manager

Lägg till [verktyget Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) i projektet. Verktyget Secret Manager lagrar känsliga data för utvecklings arbete utanför projekt trädet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden.

1. Öppna *. CSPROJ* -filen.
1. Lägg till `UserSecretsId` ett-element som det visas i följande exempel och Ersätt värdet med ditt eget, vilket vanligt vis är ett GUID:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

1. Spara filen.

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Lägg till referens till `Microsoft.Azure.AppConfiguration.AspNetCore` `Microsoft.FeatureManagement.AspNetCore` NuGet-paketen genom att köra följande kommandon:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009470001-12
    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-009000001-1251
    ```

1. Kör följande kommando för att återställa paket för ditt projekt:

    ```
    dotnet restore
    ```

1. Lägg till en hemlighet med namnet **ConnectionStrings:AppConfig** i Secret Manager.

    Den här hemligheten innehåller anslutnings strängen för att komma åt appens konfigurations arkiv. `<your_connection_string>` Ersätt värdet i följande kommando med anslutnings strängen för appens konfigurations arkiv.

    Det här kommandot måste köras i samma katalog som *.csproj*-filen.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Du använder bara Secret Manager för att testa webbappen lokalt. När du distribuerar appen till [Azure App Service](https://azure.microsoft.com/services/app-service)kan du till exempel använda en program inställning med namnet **anslutnings strängar** i App Service i stället för att använda Secret Manager för att lagra anslutnings strängen.

    Du kan komma åt den här hemligheten med appens Konfigurations-API. Ett kolon (:) fungerar i konfigurations namnet med appens Konfigurations-API på alla plattformar som stöds. Se [konfiguration efter miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Öppna *program.cs*och Lägg till en referens till .net Core app Configuration-providern:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Uppdatera metoden för att använda app-konfiguration genom att `config.AddAzureAppConfiguration()` anropa-metoden. `CreateWebHostBuilder`

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

1. Öppna *startup.cs*och Lägg till referenser till .net Core Feature Manager:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Uppdatera metoden för att lägga till stöd för funktions flaggor genom `services.AddFeatureManagement()` att anropa metoden. `ConfigureServices` Du kan också ta med alla filter som ska användas med funktions flaggor genom att anropa `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. `Configure` Uppdatera metoden för att lägga till ett mellanprogram så att funktions flagg värden kan uppdateras vid ett återkommande intervall medan ASP.net Core webbappen fortsätter att ta emot begär Anden.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. Lägg till en *MyFeatureFlags.cs* -fil:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Lägg till *BetaController.cs* i katalogen *controllers* :

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Öppna *_ViewImports. cshtml* i katalogen *vyer* och Lägg till Feature Manager-taggnings hjälpen:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Öppna *_Layout. cshtml* i den*delade* `<nav>` katalogen för *vyer*\\och ersätt streckkoden `<header>` under `<body>`  >  med följande kod:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Skapa en *beta* katalog under *vyer* och Lägg till *index. cshtml* till den:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

    ```
    dotnet build
    ```

1. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```
    dotnet run
    ```

1. Öppna ett webbläsarfönster och gå till `https://localhost:5001`, vilket är standard-URL: en för webbappen som finns lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **alla resurser**och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Välj **funktions hanteraren**och ändra status för **beta** nyckeln till **på**:

    | Nyckel | State |
    |---|---|
    | Beta | På |

1. Starta om programmet genom att växla tillbaka till kommando tolken och `Ctrl-C` fortsätta att köra `dotnet` processen genom att klicka på kör `dotnet run`igen.

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett nytt konfigurations Arkiv för appar och använt det för att hantera funktioner i en ASP.NET Core-webbapp via biblioteken för [funktions hantering](https://go.microsoft.com/fwlink/?linkid=2074664).

- Läs mer om [funktions hantering](./concept-feature-management.md).
- [Hantera funktions flaggor](./manage-feature-flags.md).
- [Använd funktions flaggor i en ASP.net Core app](./use-feature-flags-dotnet-core.md).
- [Använd dynamisk konfiguration i en ASP.NET Core app](./enable-dynamic-configuration-aspnet-core.md)
