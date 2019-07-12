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
ms.openlocfilehash: 78c64786f523aa424e8a9816e42db70e2a2997c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798459"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Självstudier: Använda dynamisk konfiguration i en ASP.NET Core-app

ASP.NET Core har en modulär konfigurationssystemet som kan läsa konfigurationsdata från olika källor. Den kan hantera ändringar i farten utan att orsaka ett program för att starta om. ASP.NET Core stöder bindning av konfigurationsinställningar till starkt typifierad .NET-klasser. Det lägger in dem i din kod med hjälp av de olika `IOptions<T>` mönster. En av dessa mönster specifikt `IOptionsSnapshot<T>`automatiskt hämtar programmets konfiguration när underliggande data ändras. Du kan mata in `IOptionsSnapshot<T>` i kontrollanter i ditt program för att få åtkomst till den senaste konfiguration som lagras i Azure App Configuration.

Du kan också ställa in App Configuration ASP.NET Core-klientbiblioteket att uppdatera en uppsättning inställningar för dynamiskt med en mellanprogram. Så länge webbappen fortsätter att ta emot begäranden, fortsätta konfigurationsinställningarna ska uppdateras med konfigurationslagringen.

För att synkronisera de inställningar som uppdateras och undvika att för många anrop till konfigurationslagringen, används en cache för varje inställning. Tills det cachelagrade värdet för en inställning har upphört att gälla, uppdateras inte uppdateringsåtgärden värdet, även om värdet har ändrats i konfigurationslagringen. Standard förfallotid för varje begäran är 30 sekunder, men den kan åsidosättas om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter Slutför [skapa en ASP.NET Core-app med Appkonfiguration](./quickstart-aspnet-core-app.md) första.

Du kan använda valfri Kodredigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgänglig på Windows, macOS och Linux-plattformar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera programmet till att uppdatera konfigurationen som svar på ändringar i en appbutik för konfigurationen.
> * Mata in den senaste konfigurationen i ditt programs domänkontrollanter.

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här självstudien måste du installera den [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

1. Öppna *Program.cs*, och uppdatera den `CreateWebHostBuilder` metod för att lägga till den `config.AddAzureAppConfiguration()` metoden.

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
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    Den `ConfigureRefresh` metod för att ange inställningar som används för att uppdatera konfigurationsdata med konfigurationslagringen appen när en uppdatering har utlösts. För att faktiskt utlösa en uppdatering, måste en uppdatering mellanprogram konfigureras för att programmet ska uppdatera konfigurationsdata när ändringar sker.

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

3. Öppna *Startup.cs*, och uppdatera den `ConfigureServices` metod för att binda konfigurationsdata till den `Settings` klass.

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

4. Uppdatera den `Configure` metod för att lägga till ett mellanprogram för att tillåta konfigurationsinställningarna som har registrerats för uppdateringen som ska uppdateras när ASP.NET Core-webbapp fortsätter att ta emot begäranden.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    Mellanprogrammet använder uppdatering-konfiguration som angetts i den `AddAzureAppConfiguration` -metod i `Program.cs` att utlösa en uppdatering för varje begäran som tas emot av ASP.NET Core-webbapp. För varje begäran, utlöses en uppdateringsåtgärden och klientbiblioteket kontrollerar om det cachelagrade värdet för de registrerade konfigurationsinställningarna har upphört att gälla. Värdena för inställningarna uppdateras med konfigurationen appbutiken för cachelagrade värdena som har upphört att gälla, och det återstående värdena förblir oförändrade.
    
    > [!NOTE]
    > Förfallotid för cache för en konfigurationsinställning är 30 sekunder, men kan åsidosättas genom att anropa den `SetCacheExpiration` metoden på initieraren alternativ skickas som ett argument till den `ConfigureRefresh` metoden.

## <a name="use-the-latest-configuration-data"></a>Använda senaste konfigurationsdata

1. Öppna *HomeController.cs* i katalogen domänkontrollanter och Lägg till en referens till den `Microsoft.Extensions.Options` paketet.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Uppdatera den `HomeController` klassen för att ta emot `Settings` via beroendeinmatning och kontrollera användning av dess värden.

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

3. Öppna *Index.cshtml* i vyerna > Home directory och Ersätt innehållet med följande skript:

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

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i Kommandotolken:

        dotnet build

2. När bygget har slutförts kör du följande kommando för att köra webbappen lokalt.

        dotnet run

3. Öppna ett webbläsarfönster och gå till `http://localhost:5000`, vilket är standard-URL för web-app som finns lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Logga in på [Azure Portal](https://portal.azure.com). Välj **alla resurser**, och välj den app configuration store-instansen som du skapade i snabbstarten.

5. Välj **Configuration Explorer**, och uppdatera värdena för följande nycklar:

    | Nyckel | Value |
    |---|---|
    | TestAppSettings:BackgroundColor | green |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Data från Azure App Configuration – nu med live-uppdateringar! |

6. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna. Mer än en uppdatering av webbläsaren kan krävas för att ändringarna visas.

    ![Snabbstart med uppdatering av lokal app](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Eftersom konfigurationsinställningarna cachelagras med en standard-förfallotid på 30 sekunder, visas alla ändringar av inställningarna i konfigurationen appbutiken endast i webbapp när cachen har upphört att gälla.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill veta mer om hur du använder Appkonfiguration, fortsätter du att Azure CLI-exempel.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)
