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
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 44ae922b182874eef378d4868fb278c3c76252db
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884420"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Självstudier: Använda dynamisk konfiguration i en ASP.NET Core-app

ASP.NET Core har ett modulärt konfigurationssystem som kan läsa konfigurationsdata från olika källor samt hantera ändringar i farten utan att göra så att ett program startar om. Det har stöd för bindning av konfigurationsinställningar till starkt typifierade .NET-klasser och inmatning av dem i din kod med hjälp av de olika `IOptions<T>`-mönstren. Ett av dessa mönster, specifikt `IOptionsSnapshot<T>`, ger automatisk återinläsning av programmets konfiguration när underliggande data ändras. Du kan mata in `IOptionsSnapshot<T>` i kontrollanter i ditt program för att få åtkomst till den senaste konfiguration som lagras i Azure App Configuration. Du kan dessutom konfigurera App Configuration ASP.NET Core-klientbiblioteket till att kontinuerligt övervaka och hämta eventuella ändringar i ett appkonfigurationsarkiv via avsökning med regelbundna intervall som du definierar.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Slutför avsnittet om att [skapa en ASP.NET Core-app med App Configuration](./quickstart-aspnet-core-app.md) först innan du fortsätter.

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är dock ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera programmet till att uppdatera konfigurationen som svar på ändringar i ett appkonfigurationsarkiv
> * Mata in den senaste konfigurationen i programmets kontrollanter

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför den här snabbstarten genom att installera [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

1. Öppna *Program.cs* och uppdatera metoden `CreateWebHostBuilder` genom att lägga till metoden `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    Den andra parametern i metoden `.Watch` är det avsökningsintervall då ASP.NET-klientbiblioteket kör frågor mot ett appkonfigurationsarkiv för att kontrollera om det har skett några ändringar av den specifika konfigurationsinställningen.

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

3. Öppna *Startup.cs* och uppdatera metoden `ConfigureServices` för att binda konfigurationsdata till klassen `Settings`.

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

## <a name="use-the-latest-configuration-data"></a>Använda senaste konfigurationsdata

1. Öppna *HomeController.cs* i katalogen *Controllers*, uppdatera klassen `HomeController` till att ta emot `Settings` via beroendeinmatning och använda dess värden.

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

2. Öppna *Index.cshtml* i katalogen *Views* > *Home* och ersätt dess innehåll med följande:

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

1. För att skapa en appversion med .NET Core CLI, kör du följande kommando i kommandogränssnittet:

        dotnet build

2. När versionen har slutförts, kör du följande kommando för att köra webbappen lokalt:

        dotnet run

3. Starta ett webbläsarfönster och gå till `http://localhost:5000`, som är standard-URL för den webapp som hanteras lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Logga in på [Azure-portalen](https://aka.ms/azconfig/portal) och klicka på **Alla resurser** och den instans av appkonfigurationsarkiv som du skapade i snabbstarten.

5. Klicka på **Key/Value Explorer** (Utforskare för nyckel/värde) och uppdatera värdena för följande nycklar:

    | Nyckel | Värde |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Data från Azure App Configuration – nu med live-uppdateringar! |

6. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    ![Snabbstart med uppdatering av lokal app](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill lära dig mer om att använda App Configuration fortsätter du till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)
