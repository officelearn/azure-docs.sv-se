---
title: Snabb start för att lägga till funktions flaggor i ASP.NET Core
description: Lägg till funktions flaggor i ASP.NET Core appar och hantera dem med Azure App konfiguration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: 12b66dc173a8d3f93f97fb369ce03533299a65d7
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235272"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Snabb start: Lägg till funktions flaggor i en ASP.NET Core app

I den här snabb starten skapar du en end-to-end-implementering av funktions hantering i ett ASP.NET Core program med Azure App konfiguration. Du kommer att använda app Configuration service för att centralt lagra alla dina funktions flaggor och kontrol lera deras tillstånd. 

Biblioteken för .NET Core Feature Management utökar ramverket med omfattande stöd för funktions flaggor. Dessa bibliotek skapas ovanpå konfigurations systemet för .NET Core. De integreras sömlöst med app-konfigurationen via sin .NET Core-Konfigurationsprovider.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [.Net Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **funktions hanteraren**  >  **+ Lägg** till för att lägga till en funktions flagga som kallas `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Aktivera funktions flagga med namnet beta](media/add-beta-feature-flag.png)

    Lämna `label` odefinierat för tillfället. Välj **Använd** för att spara den nya funktions flaggan.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Använd [.net Core kommando rads gränssnitt (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt ASP.net Core MVC-webbprogram. Fördelen med att använda .NET Core CLI i stället för Visual Studio är att .NET Core CLI är tillgängligt på Windows-, macOS-och Linux-plattformarna.

1. Skapa en ny mapp för ditt projekt. I den här snabb starten ska du ge den namnet *TestFeatureFlags*.

1. I den nya mappen kör du följande kommando för att skapa ett nytt ASP.NET Core MVC-webbprogram-projekt:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Lägga till Secret Manager

Om du vill använda Secret Manager lägger du till ett- `UserSecretsId` element i *. CSPROJ* -filen.

1. Öppna *. CSPROJ* -filen.

1.  Lägg till ett- `UserSecretsId` element som det visas här. Du kan använda samma GUID, eller så kan du ersätta det här värdet med ditt eget.

    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .net Core 3,0.  Välj rätt syntax baserat på din miljö.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. Spara *. CSPROJ* -filen.

Verktyget Secret Manager lagrar känsliga uppgifter för utvecklingsarbete utanför projektträdet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden.

> [!TIP]
> Om du vill veta mer om Secret Manager kan du läsa mer i [säker lagring av program hemligheter i utvecklingen i ASP.net Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Lägg till referens till `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet- `Microsoft.FeatureManagement.AspNetCore` paketen genom att köra följande kommandon:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Kör följande kommando för att återställa paket för ditt projekt:

    ```dotnetcli
    dotnet restore
    ```

1. Lägg till en hemlighet med namnet **ConnectionStrings:AppConfig** i Secret Manager.

    Den här hemligheten innehåller anslutnings strängen för att komma åt appens konfigurations arkiv. Ersätt `<your_connection_string>` värdet i följande kommando med anslutnings strängen för appens konfigurations arkiv. Du kan hitta den primära skrivskyddade nyckel anslutnings strängen under **åtkomst nycklar** i Azure Portal.

    Det här kommandot måste köras i samma katalog som *.csproj*-filen.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Du använder bara Secret Manager för att testa webbappen lokalt. När du distribuerar appen till [Azure App Service](https://azure.microsoft.com/services/app-service)kan du till exempel använda en program inställning med namnet **anslutnings strängar** i App Service i stället för att använda Secret Manager för att lagra anslutnings strängen.

    Du kan komma åt den här hemligheten med appens Konfigurations-API. Ett kolon (:) fungerar i konfigurations namnet med appens Konfigurations-API på alla plattformar som stöds. Se [konfiguration efter miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. I *program.cs*uppdaterar du `CreateWebHostBuilder` metoden för att använda app-konfiguration genom att anropa `config.AddAzureAppConfiguration()` metoden.

    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .net Core 3,0.  Välj rätt syntax baserat på din miljö.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

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

1. Öppna *startup.cs*och Lägg till referenser till .net Core Feature Manager:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Uppdatera `ConfigureServices` metoden för att lägga till stöd för funktions flaggor genom att anropa `services.AddFeatureManagement()` metoden. Du kan också ta med alla filter som ska användas med funktions flaggor genom att anropa `services.AddFeatureFilter<FilterType>()` :

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
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
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
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

1. Öppna *_ViewImports. cshtml* i katalogen *vyer* och Lägg till hjälp för Feature Manager-tagg:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Öppna *_Layout. cshtml* i den *Views* \\ *delade* katalogen för vyer och Ersätt `<nav>` streckkoden under `<body>`  >  `<header>` med följande kod:

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

1. Öppna ett webbläsarfönster och gå till `https://localhost:5000` , vilket är standard-URL: en för webbappen som finns lokalt.
    Om du arbetar i Azure Cloud Shell väljer du knappen för *förhands granskning* följt av *Konfigurera*.  När du uppmanas väljer du port 5000.

    ![Leta upp knappen Förhandsgranska för webben](./media/quickstarts/cloud-shell-web-preview.png)

    Webbläsaren ska visa en sida som liknar bilden nedan.
    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser**och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Välj **funktions hanteraren**och ändra statusen för **beta** nyckeln till **på**.

1. Återgå till kommando tolken och Avbryt `dotnet` processen som körs genom att trycka på `Ctrl-C` .  Starta om programmet med `dotnet run` .

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett nytt konfigurations Arkiv för appar och använt det för att hantera funktioner i en ASP.NET Core-webbapp via [biblioteken för funktions hantering](https://go.microsoft.com/fwlink/?linkid=2074664).

- Läs mer om [funktions hantering](./concept-feature-management.md).
- [Hantera funktions flaggor](./manage-feature-flags.md).
- [Använd funktions flaggor i en ASP.net Core app](./use-feature-flags-dotnet-core.md).
- [Använda dynamisk konfiguration i en ASP.NET Core-app](./enable-dynamic-configuration-aspnet-core.md)
