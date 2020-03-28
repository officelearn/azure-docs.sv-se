---
title: 'Självstudiekurs: Använda dynamisk konfiguration av appkonfiguration i ASP.NET Core'
titleSuffix: Azure App Configuration
description: I den här självstudien lär du dig att dynamiskt uppdatera konfigurationsdata för ASP.NET Core-appar
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
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: e9df6d2e7a8219d16e7b60f7c3b8d826a87e6110
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348858"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Självstudiekurs: Använda dynamisk konfiguration i en ASP.NET Core-app

ASP.NET Core har ett inkopplat konfigurationssystem som kan läsa konfigurationsdata från en mängd olika källor. Det kan hantera ändringar dynamiskt utan att orsaka ett program att starta om. ASP.NET Core stöder bindning av konfigurationsinställningar till starkt skrivna .NET-klasser. Det injicerar dem i din `IOptions<T>` kod med hjälp av de olika mönstren. Ett av dessa mönster, `IOptionsSnapshot<T>`specifikt, laddar automatiskt om programmets konfiguration när de underliggande data ändras. Du kan mata in `IOptionsSnapshot<T>` i kontrollanter i ditt program för att få åtkomst till den senaste konfiguration som lagras i Azure App Configuration.

Du kan också ställa in appkonfigurationen ASP.NET Core-klientbiblioteket för att uppdatera en uppsättning konfigurationsinställningar dynamiskt med hjälp av ett mellanprogram. Konfigurationsinställningarna uppdateras med konfigurationsarkivet varje gång så länge webbappen tar emot begäranden.

Appkonfiguration cachelagrar automatiskt varje inställning för att undvika för många anrop till konfigurationsarkivet. Uppdateringsåtgärden väntar tills det cachelagrade värdet för en inställning upphör att uppdatera den inställningen, även när dess värde ändras i konfigurationsarkivet. Standardtiden för förfallotiden för cachen är 30 sekunder. Du kan åsidosätta den här förfallotiden om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter slutför du [Skapa en ASP.NET Core-app med appkonfiguration](./quickstart-aspnet-core-app.md) först.

Du kan använda vilken kodredigerare som helst för att göra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformarna.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera programmet för att uppdatera konfigurationen som svar på ändringar i ett App Configuration Store.
> * Injicera den senaste konfigurationen i programmets handkontroller.

## <a name="prerequisites"></a>Krav

Om du vill göra den här självstudien installerar du [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Innan du fortsätter slutför du [Skapa en ASP.NET Core-app med appkonfiguration](./quickstart-aspnet-core-app.md) först.

## <a name="add-a-sentinel-key"></a>Lägga till en sentinelnyckel

En *indikatornyckel* är en speciell nyckel som används för att signalera när konfigurationen har ändrats. Appen övervakar indikatornyckeln för ändringar. När en ändring upptäcks uppdaterar du alla konfigurationsvärden. Den här metoden minskar det totala antalet begäranden som görs av din app till appkonfiguration, jämfört med att övervaka alla nycklar för ändringar.

1. I Azure-portalen väljer du **Konfigurationsutforskaren > Skapa > nyckelvärde**.

1. För **Nyckel**anger du *TestApp:Settings:Sentinel*. För **Värde**anger du 1. Lämna **etikett** och **innehållstyp** tom.

1. Välj **Använd**.

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

1. Lägg till en `Microsoft.Azure.AppConfiguration.AspNetCore` referens till NuGet-paketet genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Öppna *Program.cs*och uppdatera `CreateWebHostBuilder` metoden för `config.AddAzureAppConfiguration()` att lägga till metoden.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
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
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    Metoden `ConfigureRefresh` används för att ange de inställningar som används för att uppdatera konfigurationsdata med App Configuration Store när en uppdateringsåtgärd utlöses. Parametern `refreshAll` till `Register` metoden anger att alla konfigurationsvärden ska uppdateras om indikatornyckeln ändras.

    `SetCacheExpiration` Metoden åsidosätter dessutom standardcachens förfallotid på 30 sekunder och anger en tid på 5 minuter i stället. Detta minskar antalet begäranden som görs till appkonfiguration.

    > [!NOTE]
    > I testsyfte kanske du vill sänka cachetidens utgångstid.

    Om du faktiskt vill utlösa en uppdateringsåtgärd måste du konfigurera en uppdatering mellanprogram för att programmet ska kunna uppdatera konfigurationsdata när en ändring inträffar. Du får se hur du gör detta i ett senare steg.

2. Lägg till en *Settings.cs*-fil som definierar och implementerar en ny `Settings`-klass.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Öppna *Startup.cs*och använd `IServiceCollection.Configure<T>` i `ConfigureServices` metoden för att `Settings` binda konfigurationsdata till klassen.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Uppdatera `Configure` metoden och `UseAzureAppConfiguration` lägg till mellanprogram så att konfigurationsinställningarna som registrerats för uppdatering kan uppdateras medan ASP.NET Core-webbappen fortsätter att ta emot begäranden.


    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    Mellanmaterialet använder den uppdateringskonfiguration `AddAzureAppConfiguration` som `Program.cs` anges i metoden för att utlösa en uppdatering för varje begäran som tas emot av ASP.NET Core-webbappen. För varje begäran utlöses en uppdateringsåtgärd och klientbiblioteket kontrollerar om cachelagrat värde för den registrerade konfigurationsinställningen har upphört att gälla. Om den har gått ut uppdateras den.

## <a name="use-the-latest-configuration-data"></a>Använda senaste konfigurationsdata

1. Öppna *HomeController.cs* i katalogen Controllers och lägg till `Microsoft.Extensions.Options` en referens till paketet.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Uppdatera `HomeController` klassen för `Settings` att ta emot genom beroendeinjektion och använd dess värden.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. Öppna *Index.cshtml* i katalogen Vyer > hem och ersätt innehållet med följande skript:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommandoskalet:

        dotnet build

1. När versionen har slutförts kör du följande kommando för att köra webbappen lokalt:

        dotnet run
1. Öppna ett webbläsarfönster och gå till `dotnet run` webbadressen som visas i utdata.

    ![Starta snabbstartsapp lokalt](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser**och välj den App Configuration Store-instans som du skapade i snabbstarten.

1. Välj **Konfigurationsutforskaren**och uppdatera värdena för följande nycklar:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Data från Azure App Configuration – nu med live-uppdateringar! |
    | TestApp:Inställningar:Sentinel | 2 |

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna. Du kan behöva uppdatera mer än en gång för att ändringarna ska återspeglas.

    ![Starta uppdaterad snabbstartsapp lokalt](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat din ASP.NET Core-webbapp för att dynamiskt uppdatera konfigurationsinställningarna från AppKonfiguration. Om du vill veta hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till appkonfiguration fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Hanterad identitetsintegrering](./howto-integrate-azure-managed-service-identity.md)
