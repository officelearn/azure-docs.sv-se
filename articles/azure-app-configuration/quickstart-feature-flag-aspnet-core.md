---
title: Snabbstart för att lägga till funktionsflaggor i ASP.NET Core
description: Lägga till funktionsflaggor för att ASP.NET Core-appar och hantera dem med Azure App-konfiguration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: b3579d12981e2b0add916a280bac7b4f9392d8ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803151"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Snabbstart: Lägga till funktionsflaggor i en ASP.NET Core-app

I den här snabbstarten skapar du en implementering från på 1-2 i ett ASP.NET Core-program med Azure App Configuration. Du kommer att använda tjänsten Appkonfiguration för att centralt lagra alla dina funktionsflaggor och styra deras tillstånd. 

.NET Core Feature Management-biblioteken utökar ramverket med omfattande stöd för funktionsflagga. Dessa bibliotek är byggda ovanpå .NET Core-konfigurationssystemet. De integreras sömlöst med App-konfiguration via .NET Core-konfigurationsprovidern.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Funktionshanteraren** > **+Lägg** till `Beta`om du vill lägga till en funktionsflagga med namnet .

    > [!div class="mx-imgBorder"]
    > ![Aktivera funktionsflagga med namnet Beta](media/add-beta-feature-flag.png)

    Lämna `label` odefinierad för tillfället. Välj **Använd** om du vill spara den nya funktionsflaggan.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Använd [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt ASP.NET Core MVC-webbappprojekt. Fördelen med att använda .NET Core CLI i stället för Visual Studio är att .NET Core CLI är tillgängligt på windows-, macOS- och Linux-plattformarna.

1. Skapa en ny mapp för ditt projekt. För denna snabbstart, namn det *TestFeatureFlags*.

1. I den nya mappen kör du följande kommando för att skapa ett nytt ASP.NET Core MVC-webbappprojekt:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Lägga till Secret Manager

Om du vill använda `UserSecretsId` Secret Manager lägger du till ett element i *CSproj-filen.*

1. Öppna *CSproj-filen.*

1.  Lägg `UserSecretsId` till ett element som visas här. Du kan använda samma GUID eller ersätta det här värdet med ditt eget.

    > [!IMPORTANT]
    > `CreateHostBuilder`ersätts `CreateWebHostBuilder` i .NET Core 3.0.  Välj rätt syntax baserat på din miljö.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. Spara *CSproj-filen.*

Verktyget Secret Manager lagrar känsliga uppgifter för utvecklingsarbete utanför projektträdet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden.

> [!TIP]
> Mer information om Secret Manager finns [i Säker lagring av apphemligheter under utveckling i ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Lägg till `Microsoft.Azure.AppConfiguration.AspNetCore` referens `Microsoft.FeatureManagement.AspNetCore` till och NuGet-paketen genom att köra följande kommandon:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Kör följande kommando för att återställa paket för projektet:

    ```dotnetcli
    dotnet restore
    ```

1. Lägg till en hemlighet med namnet **ConnectionStrings:AppConfig** i Secret Manager.

    Den här hemligheten innehåller anslutningssträngen för att komma åt appkonfigurationsarkivet. Ersätt `<your_connection_string>` värdet i följande kommando med anslutningssträngen för App Configuration Store. Du hittar anslutningssträngen under **Åtkomstnycklar** i Azure-portalen.

    Det här kommandot måste köras i samma katalog som *.csproj*-filen.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Du använder Secret Manager endast för att testa webbappen lokalt. När du distribuerar appen till [Azure App Service,](https://azure.microsoft.com/services/app-service)till exempel, använder du en programinställning med namnet **Anslutningssträngar** i App-tjänsten i stället för att använda Secret Manager för att lagra anslutningssträngen.

    Du kan komma åt den här hemligheten med API:et för appkonfiguration. Ett kolon (:) fungerar i konfigurationsnamnet med API:et för appkonfiguration på alla plattformar som stöds. Se [Konfiguration efter miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. I *Program.cs*uppdaterar du `CreateWebHostBuilder` metoden för att använda `config.AddAzureAppConfiguration()` Appkonfiguration genom att anropa metoden.

    > [!IMPORTANT]
    > `CreateHostBuilder`ersätts `CreateWebHostBuilder` i .NET Core 3.0.  Välj rätt syntax baserat på din miljö.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. Öppna *Startup.cs*och lägg till referenser till funktionshanteraren för .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Uppdatera `ConfigureServices` metoden för att lägga till `services.AddFeatureManagement()` stöd för funktionsflagga genom att anropa metoden. Du kan också inkludera alla filter som ska `services.AddFeatureFilter<FilterType>()`användas med funktionsflaggor genom att ringa:

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }

    ---

1. Update the `Configure` method to add a middleware to allow the feature flag values to be refreshed at a recurring interval while the ASP.NET Core web app continues to receive requests.

    #### [.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. Lägga *MyFeatureFlags.cs* till en MyFeatureFlags.cs-fil:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Lägg till *BetaController.cs* i katalogen *Controllers:*

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

1. Öppna *_ViewImports.cshtml* i katalogen *Vyer* och lägg till hjälpen för funktionshanterarens tagghjälp:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Öppna *_Layout.cshtml* i katalogen*Delade* *vyer*\\och `<nav>` ersätt `<body>`  >  `<header>` streckkoden under med följande kod:

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

1. Skapa en *betakatalog* under *Vyer* och lägg till *Index.cshtml* i den:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommandoskalet:

    ```
    dotnet build
    ```

1. När versionen har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```
    dotnet run
    ```

1. Öppna ett webbläsarfönster och `https://localhost:5000`gå till , som är standard-URL:en för webbappen lokalt.
    Om du arbetar i Azure Cloud Shell väljer du knappen *Förhandsgranska följt* av *Konfigurera*.  Välj port 5000 när du uppmanas att göra det.

    ![Leta reda på knappen Förhandsgranska](./media/quickstarts/cloud-shell-web-preview.png)

    Din webbläsare bör visa en sida som liknar bilden nedan.
    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser**och välj den App Configuration Store-instans som du skapade i snabbstarten.

1. Välj **Funktionshanteraren**och ändra **betanyckelns** tillstånd till **På**.

1. Återgå till kommandotolken och `dotnet` avbryt `Ctrl-C`den pågående processen genom att trycka på .  Starta om `dotnet run`programmet med .

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny App Configuration Store och använde den för att hantera funktioner i en ASP.NET Core-webbapp via [funktionshanteringsbiblioteken](https://go.microsoft.com/fwlink/?linkid=2074664).

- Läs mer om [funktionshantering](./concept-feature-management.md).
- [Hantera funktionsflaggor](./manage-feature-flags.md).
- [Använd funktionsflaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md).
- [Använda dynamisk konfiguration i en ASP.NET Core-app](./enable-dynamic-configuration-aspnet-core.md)
