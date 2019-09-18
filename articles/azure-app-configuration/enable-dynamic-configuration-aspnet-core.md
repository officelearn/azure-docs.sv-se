---
title: Självstudie om användning av dynamisk konfiguration av Azure App Configuration i en ASP.NET Core-app | Microsoft Docs
description: I den här självstudien lär du dig att dynamiskt uppdatera konfigurationsdata för ASP.NET Core-appar
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 235b55bcd727e3e3ea947ce086209e0a94f70752
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076389"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Självstudier: Använda dynamisk konfiguration i en ASP.NET Core-app

ASP.NET Core har ett anslutnings Bart konfigurations system som kan läsa konfigurations data från en mängd olika källor. Den kan hantera ändringar i farten utan att göra det möjligt för ett program att starta om. ASP.NET Core stöder bindning av konfigurations inställningar till starkt skrivna .NET-klasser. Den infogar dem i koden med hjälp av de olika `IOptions<T>` mönstren. Ett av dessa mönster, i `IOptionsSnapshot<T>`synnerhet, laddar automatiskt om programmets konfiguration när underliggande data ändras. Du kan mata in `IOptionsSnapshot<T>` i kontrollanter i ditt program för att få åtkomst till den senaste konfiguration som lagras i Azure App Configuration.

Du kan också konfigurera appens konfiguration ASP.NET Core klient bibliotek för att uppdatera en uppsättning konfigurations inställningar dynamiskt med hjälp av ett mellanprogram. Så länge webbappen fortsätter att ta emot begär Anden, fortsätter konfigurations inställningarna att uppdateras med konfigurations lagret.

För att hålla inställningarna uppdaterade och undvika för många anrop till konfigurations arkivet används ett cacheminne för varje inställning. Tills det cachelagrade värdet för en inställning har gått ut uppdateras inte värdet, även om värdet har ändrats i konfigurations arkivet. Standard förfallo tiden för varje begäran är 30 sekunder, men den kan åsidosättas om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter måste du först [skapa en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ditt program för att uppdatera konfigurationen som svar på ändringar i ett konfigurations lager för appar.
> * Mata in den senaste konfigurationen i dina programs kontrollanter.

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här själv studie kursen installerar du [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

1. Öppna *program.cs*och uppdatera `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` metoden för att lägga till-metoden.

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
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    `ConfigureRefresh` Metoden används för att ange inställningarna som används för att uppdatera konfigurations data med appens konfigurations Arkiv när en uppdatering aktive ras. För att en uppdatering ska kunna aktive ras måste ett uppdaterings program vara konfigureras för att programmet ska kunna uppdatera konfigurations data när en ändring sker.

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

3. Öppna *startup.cs*och uppdatera `ConfigureServices` metoden för att binda `Settings` konfigurations data till klassen.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. `Configure` Uppdatera metoden för att lägga till ett mellanprogram så att de konfigurations inställningar som registrerats för uppdatering uppdateras medan den ASP.net Core webbappen fortsätter att ta emot begär Anden.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    Mellanprogram använder den uppdaterings konfiguration som anges i `AddAzureAppConfiguration` -metoden `Program.cs` i för att utlösa en uppdatering för varje begäran som tas emot av ASP.net Core webbappen. För varje begäran utlöses en uppdaterings åtgärd och klient biblioteket kontrollerar om det cachelagrade värdet för de registrerade konfigurations inställningarna har upphört att gälla. För de cachelagrade värdena som har upphört att gälla uppdateras värdena för inställningarna med appens konfigurations Arkiv och de återstående värdena förblir oförändrade.
    
    > [!NOTE]
    > Standard-cachens förfallo tid för en konfigurations inställning är 30 sekunder, men kan åsidosättas genom att `SetCacheExpiration` anropa metoden på Options-initieraren som skickades som `ConfigureRefresh` ett argument till metoden.

## <a name="use-the-latest-configuration-data"></a>Använda senaste konfigurationsdata

1. Öppna *HomeController.cs* i katalogen controllers och Lägg till en referens i `Microsoft.Extensions.Options` paketet.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Uppdatera klassen som ska tas `Settings` emot via beroende insprutning och använd dess värden. `HomeController`

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

3. Öppna *index. cshtml* i vyerna > Home Directory och ersätt innehållet med följande skript:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
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

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

        dotnet build

2. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

        dotnet run

3. Öppna ett webbläsarfönster och gå till `http://localhost:5000`, vilket är standard-URL: en för webbappen som finns lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Logga in på [Azure Portal](https://portal.azure.com). Välj **alla resurser**och välj den instans av app Configuration Store som du skapade i snabb starten.

5. Välj **Configuration Explorer**och uppdatera värdena för följande nycklar:

    | Nyckel | Value |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Data från Azure App Configuration – nu med live-uppdateringar! |

6. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna. Mer än en uppdatering av webb sidan kan krävas för att ändringarna ska visas.

    ![Snabbstart med uppdatering av lokal app](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Eftersom konfigurations inställningarna cachelagras med en förfallo tid på 30 sekunder, kommer alla ändringar som görs i inställningarna i appens konfigurations Arkiv bara att avspeglas i webbappen när cachen har upphört att gälla.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill veta mer om hur du använder app-konfiguration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)
