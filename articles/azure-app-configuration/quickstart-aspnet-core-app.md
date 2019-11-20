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
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 91712b3f730317e65cda7b48c8f5636b2fb9ab2c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185094"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Snabb start: skapa en ASP.NET Core-app med Azure App konfiguration

I den här snabb starten inkluderar du Azure App konfiguration i en ASP.NET Core-app för att centralisera lagring och hantering av program inställningar separat från din kod. ASP.NET Core skapar ett enda nyckelbaserade konfigurations objekt genom att använda inställningar från en eller flera data källor som anges av ett program. Dessa data källor kallas för *konfigurations leverantörer*. Eftersom app Configurations .NET Core-klient implementeras som en sådan Provider, visas tjänsten som en annan data källa.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **konfigurations utforskaren** >  **+ skapa** för att lägga till följande nyckel/värde-par:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:BackgroundColor | Vit |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Svart |
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Du använder [.net Core kommando rads gränssnitt (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt ASP.net Core MVC-webbprogram. Fördelen med att använda .NET Core CLI i Visual Studio är att den är tillgänglig på Windows-, macOS-och Linux-plattformarna.

1. Skapa en ny mapp för ditt projekt. I den här snabb starten ska du ge den namnet *TestAppConfig*.

2. I den nya mappen kör du följande kommando för att skapa ett nytt ASP.NET Core MVC-webbprogram-projekt:

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Lägga till Secret Manager

Om du vill använda Secret Manager lägger du till ett `UserSecretsId`-element i *. CSPROJ* -filen.

- Öppna *. CSPROJ* -filen. Lägg till ett `UserSecretsId`-element som det visas här. Du kan använda samma GUID, eller så kan du ersätta det här värdet med ditt eget. Spara filen.

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

Verktyget Secret Manager lagrar känsliga uppgifter för utvecklingsarbete utanför projektträdet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden. Mer information om Secret Manager finns i [säker lagring av app hemligheter i utvecklingen i ASP.net Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Lägg till en referens till `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-paketet genom att köra följande kommando:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Kör följande kommando för att återställa paket för ditt projekt:

    ```CLI
        dotnet restore
    ```
3. Lägg till en hemlighet med namnet *ConnectionStrings:AppConfig* i Secret Manager.

    Den här hemligheten innehåller anslutnings strängen för att komma åt appens konfigurations arkiv. Ersätt värdet i följande kommando med anslutnings strängen för appens konfigurations arkiv.

    Det här kommandot måste köras i samma katalog som *.csproj*-filen.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Vissa gränssnitt kommer att trunkera anslutnings strängen om den inte omges av citat tecken. Se till att utdata från kommandot `dotnet user-secrets` visar hela anslutnings strängen. Om den inte gör det kör du kommandot på nytt, så att anslutnings strängen stängs av i citat tecken.

    Secret Manager används bara för att testa webbappen lokalt. När appen distribueras till [Azure App Service](https://azure.microsoft.com/services/app-service/web)kan du till exempel använda ett program för att ange **anslutnings strängar** i App Service i stället för med Secret Manager för att lagra anslutnings strängen.

    Den här hemligheten nås med Konfigurations-API: et. Ett kolon (:) fungerar i konfigurations namnet med Konfigurations-API: et på alla plattformar som stöds. Se [konfiguration efter miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Öppna *program.cs*och Lägg till en referens till .net Core app Configuration-providern.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Uppdatera `CreateWebHostBuilder`-metoden för att använda app-konfiguration genom att anropa `config.AddAzureAppConfiguration()`-metoden.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .NET Core 3,0.  Välj rätt syntax baserat på din miljö.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Uppdatera `CreateWebHostBuilder` för .NET Core 2. x

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

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>Uppdatera `CreateHostBuilder` för .NET Core 3. x

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

6. Öppna *index. cshtml* i vyerna > Home Directory och ersätt innehållet med följande kod:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Öppna *_Layout. cshtml* i vyerna > delade katalogen och ersätt innehållet med följande kod:

    ```HTML
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

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

    ```CLI
       dotnet build
    ```

2. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```CLI
        dotnet run
    ```

3. Öppna ett webbläsarfönster och gå till `http://localhost:5000`, vilket är standard-URL: en för webbappen som finns lokalt.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett nytt konfigurations Arkiv för appar och använt det med en ASP.NET Core-webbapp via [appens Konfigurationsprovider](https://go.microsoft.com/fwlink/?linkid=2074664). Fortsätt till nästa självstudie om du vill veta hur du konfigurerar din ASP.NET Core-app för att dynamiskt uppdatera konfigurations inställningar.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-aspnet-core.md)
