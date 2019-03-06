---
title: Snabbstart för Azure App Configuration med ASP.NET Core | Microsoft Docs
description: En snabbstart för användning av Azure App Configuration med ASP.NET Core-appar
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: ce19041b29d567f061dde59fbe041adf61f889a0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961490"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Snabbstart: Skapa en ASP.NET Core-app med Azure App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Med den kan du enkelt lagra och hantera alla programinställningar på ett ställe som är separat från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en ASP.NET Core-webbapp. ASP.NET Core skapar ett enda nyckelvärdesbaserat konfigurationsobjekt med hjälp av inställningar från en eller flera datakällor, som kallas *konfigurationsprovidrar*, som anges av ett program. Eftersom App Configurations .NET Core-klient är implementerad som en sådan provider visas tjänsten precis som andra datakällor.

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är dock ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför den här snabbstarten genom att installera [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Du använder [.NET Core-kommandoradsgränssnittet (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt ASP.NET Core MVC-webbapprojekt. Fördelen med att använda .NET Core CLI istället för Visual Studio är att det finns tillgängligt för både Windows-, macOS- och Linux-plattformar.

1. Skapa en ny mapp för ditt projekt. För den här snabbstarten ger vi den namnet *TestAppConfig*.

2. Kör följande kommando i den nya mappen för att skapa ett nytt ASP.NET Core MVC-webbapp-projekt:

        dotnet new mvc

## <a name="add-secret-manager"></a>Lägga till Secret Manager

Du lägger till [verktyget Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) i projektet. Verktyget Secret Manager lagrar känsliga uppgifter för utvecklingsarbete utanför projektträdet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden.

- Öppna *.csproj*-filen. Lägg till ett `UserSecretsId`-element såsom det visas nedan och ersätt dess värde med ditt eget, vilket vanligtvis är ett GUID. Spara filen.

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

## <a name="connect-to-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Lägg till en referens till NuGet-paketet `Microsoft.Extensions.Configuration.AzureAppConfiguration` genom att köra följande kommando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Kör följande kommando för att återställa paket för projektet.

        dotnet restore

3. Lägg till en hemlighet med namnet *ConnectionStrings:AppConfig* i Secret Manager.

    Den här hemligheten kommer att innehålla anslutningssträngen för åtkomst till ditt appkonfigurationsarkiv. Ersätt värdet i kommandot nedan med anslutningssträngen för ditt appkonfigurationsarkiv.

    Det här kommandot måste köras i samma katalog som *.csproj*-filen.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    Secret Manager används endast för testning av webbappen lokalt. När appen har distribuerats (till exempel till [Azure App Service](https://azure.microsoft.com/services/app-service/web)) kommer du att använda en programinställning (till exempel **anslutningssträngar** i App Service) i stället för att lagra anslutningssträngen med Secret Manager.

    Du får tillgång till den här hemligheten med konfigurations-API:et. Ett kolon (:) fungerar i konfigurationsnamnet med API-konfigurationen på alla plattformar som stöds. Se dokumentationen om [konfiguration efter miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0#configuration-by-environment).

4. Öppna *Program.cs* och uppdatera metoden `CreateWebHostBuilder` till att använda App Configuration genom att anropa metoden `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

5. Öppna *Index.cshtml* i katalogen *Views* > *Home* och ersätt dess innehåll med följande kod:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. Öppna *_Layout.cshtml* i katalogen *Views* > *Shared* och ersätt dess innehåll med följande kod:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. För att skapa en appversion med .NET Core CLI, kör du följande kommando i kommandogränssnittet:

        dotnet build

2. När versionen har slutförts, kör du följande kommando för att köra webbappen lokalt:

        dotnet run

3. Starta ett webbläsarfönster och gå till `http://localhost:5000`, som är standard-URL för den webapp som hanteras lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett nytt appkonfigurationsarkiv och använt det med en ASP.NET Core-webbapp. Om du vill lära dig mer om att använda App Configuration fortsätter du till nästa självstudie som visar hur autentisering går till.

> [!div class="nextstepaction"]
> [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
