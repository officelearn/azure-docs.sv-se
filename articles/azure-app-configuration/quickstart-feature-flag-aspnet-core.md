---
title: Snabbstart för att lägga till funktionen flaggor i ASP.NET Core | Microsoft Docs
description: En Snabbstart för att lägga till funktionen flaggor i ASP.NET Core-appar och hantera dem i Azure-Appkonfiguration
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
ms.openlocfilehash: 38b404ec10fb7b66b5e276665b0c9047d0576c15
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798393"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Snabbstart: Lägga till funktionen flaggor i en ASP.NET Core-app

Du kan aktivera funktionen för hantering i ASP.NET Core genom att ansluta ditt program till Azure-Appkonfiguration. Du kan använda den här hanterade tjänsten för att lagra alla dina flaggor för funktionen och kontrollera deras tillstånd centralt. Den här snabbstarten visar hur du införlivar Appkonfiguration i en ASP.NET Core webbapp för att skapa en slutpunkt till slutpunkt-implementering av hantering av funktionen.

Hanteringsbibliotek för .NET Core funktionen utöka framework med omfattande funktioner som stöds med flaggan. Dessa bibliotek är byggda på konfigurationssystemet för .NET Core. De integrera sömlöst med konfiguration via dess konfigurationsprovider för .NET Core.

Du kan använda valfri Kodredigerare för att utföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ tillgängligt på Windows, macOS och Linux-plattformar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här snabbstarten måste du installera den [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett Arkiv för Appkonfiguration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **funktionen Manager** >  **+ skapa** att lägga till följande funktion flaggor:

    | Nyckel | Tillstånd |
    |---|---|
    | Beta | Av |

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Du använder den [.NET Core-kommandoradsgränssnittet (CLI)](https://docs.microsoft.com/dotnet/core/tools/) att skapa ett nytt ASP.NET Core MVC web app-projekt. Fördelen med att använda .NET Core CLI istället för Visual Studio är att .NET Core CLI är tillgänglig i Windows, macOS och Linux-plattformar.

1. Skapa en ny mapp för ditt projekt. Den här snabbstarten, ge den namnet *TestFeatureFlags*.

1. Kör följande kommando för att skapa ett nytt ASP.NET Core MVC web app-projekt i den nya mappen:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Lägga till Secret Manager

Lägg till den [Secret Manager verktyget](https://docs.microsoft.com/aspnet/core/security/app-secrets) i projektet. Verktyget Secret Manager lagrar känsliga data för utvecklingsarbete utanför projektträdet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden.

1. Öppna den *.csproj* fil.
1. Lägg till en `UserSecretsId` element som visas i följande exempel och Ersätt värdet med dina egna, vilket vanligtvis är ett GUID:

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

## <a name="connect-to-an-app-configuration-store"></a>Anslut till en konfiguration av store

1. Lägg till referens till den `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-paketet genom att köra följande kommando:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7
    ```

1. Kör följande kommando för att återställa paketen för ditt projekt:

    ```
    dotnet restore
    ```

1. Lägg till en hemlighet med namnet **ConnectionStrings:AppConfig** i Secret Manager.

    Den här hemligheten innehåller anslutningssträngen för att komma åt din Appkonfiguration store. Ersätt den `<your_connection_string>` värde i följande kommando med anslutningssträngen för din konfiguration av store.

    Det här kommandot måste köras i samma katalog som *.csproj*-filen.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Du kan använda Secret Manager enbart för att testa webbappen lokalt. När du distribuerar appen till [Azure App Service](https://azure.microsoft.com/services/app-service), exempelvis kan du använda ett program som inställning med namnet **anslutningssträngar** i App Service i stället för med Secret Manager för att lagra anslutningssträngen.

    Du kan komma åt den här hemligheten med konfigurations-API-App. Ett kolon (:) fungerar i Konfigurationsnamnet med API: et för App-konfigurationen på alla plattformar som stöds. Se [konfigurationen av miljön](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Öppna *Program.cs*, och Lägg till en referens till Appkonfiguration för .NET Core-providern:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Uppdatera den `CreateWebHostBuilder` metod du använder Appkonfiguration genom att anropa den `config.AddAzureAppConfiguration()` metoden.

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

1. Öppna *Startup.cs*, och Lägg till referenser till .NET Core funktionen manager:

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

1. Uppdatera den `ConfigureServices` metod för att lägga till stöd för funktionsflagga genom att anropa den `services.AddFeatureManagement()` metoden. Du kan ta något filter som ska användas med funktionen flaggor genom att anropa `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Uppdatera den `Configure` metod för att lägga till ett mellanprogram för att tillåta funktionen flaggvärden som ska uppdateras med ett återkommande intervall när ASP.NET Core-webbapp fortsätter att ta emot begäranden.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. Lägg till en *MyFeatureFlags.cs* fil:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Lägg till *BetaController.cs* till den *domänkontrollanter* directory:

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

1. Öppna *_ViewImports.cshtml* i den *vyer* directory, och Lägg till tagg-helper funktionen manager:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Öppna *_Layout.cshtml* i den *vyer*\\*delad* directory och Ersätt den `<nav>` streckkod under `<body>`  >  `<header>` med följande kod:

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

1. Skapa en *Beta* katalogen under *vyer* och Lägg till *Index.cshtml* till den:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i Kommandotolken:

    ```
    dotnet build
    ```

1. När bygget har slutförts kör du följande kommando för att köra webbappen lokalt.

    ```
    dotnet run
    ```

1. Öppna ett webbläsarfönster och gå till `https://localhost:5001`, vilket är standard-URL för web-app som finns lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **alla resurser**, och välj den konfiguration av store-instans som du skapade i snabbstarten.

1. Välj **funktionen Manager**, och ändrar status för den **Beta** avgörande för att **på**:

    | Nyckel | Tillstånd |
    |---|---|
    | Beta | På |

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en ny konfiguration av butik och använt det för att hantera funktioner i en ASP.NET Core-webbapp via den [funktionen hanteringsbibliotek](https://go.microsoft.com/fwlink/?linkid=2074664).

- Läs mer om [funktionen management](./concept-feature-management.md).
- [Hantera flaggor för funktionen](./manage-feature-flags.md).
- [Använder funktionen flaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md).
