---
title: Snabbstart för Azure App Configuration med ASP.NET Core | Microsoft Docs
description: Snabbstart för att använda Azure App-konfiguration med ASP.NET Core-appar
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 537dabe09c41012b9e15998ce3af8198dcfb62d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245782"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Snabbstart: Skapa en ASP.NET Core-app med Azure App-konfiguration

I den här snabbstarten använder du Azure App-konfiguration för att centralisera lagring och hantering av programinställningar för ett ASP.NET Core-program. ASP.NET Core skapar ett enda nyckelvärdesbaserat konfigurationsobjekt med inställningar från en eller flera datakällor som anges av ett program. Dessa datakällor kallas *konfigurationsleverantörer*. Eftersom App Configurations .NET Core-klient implementeras som konfigurationsleverantör visas tjänsten som en annan datakälla.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> Azure Cloud Shell är ett kostnadsfritt interaktivt skal som du kan använda för att köra kommandoradsinstruktionerna i den här artikeln.  Den har vanliga Azure-verktyg förinstallerade, inklusive .NET Core SDK. Om du är inloggad på din Azure-prenumeration startar du ditt [Azure Cloud Shell](https://shell.azure.com) från shell.azure.com.  Du kan läsa mer om Azure Cloud Shell genom [att läsa vår dokumentation](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj**Skapa** > nyckelvärde för **konfigurationsutforskaren** > om du vill lägga till följande nyckel-värde-par:**Key-value**

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:BackgroundColor | Vit |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Svart |
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etikett** och **innehållstyp** tomma för tillfället. Välj **Använd**.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Använd [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt ASP.NET Core MVC-webbappprojekt. [Azure Cloud Shell](https://shell.azure.com) tillhandahåller dessa verktyg för dig.  De är också tillgängliga på windows-, macOS- och Linux-plattformarna.

1. Skapa en ny mapp för ditt projekt. För den här snabbstarten namnger du den *TestAppConfig*.

1. I den nya mappen kör du följande kommando för att skapa ett nytt ASP.NET Core MVC-webbappprojekt:

```dotnetcli
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
> Mer information om Secret Manager finns [i Säker lagring av apphemligheter under utveckling i ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Lägg till en `Microsoft.Azure.AppConfiguration.AspNetCore` referens till NuGet-paketet genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Kör följande kommando för att återställa paket för projektet:

    ```dotnetcli
    dotnet restore
    ```

1. Lägg till en hemlighet med namnet *ConnectionStrings:AppConfig* i Secret Manager.

    Den här hemligheten innehåller anslutningssträngen för att komma åt appkonfigurationsarkivet. Ersätt värdet i följande kommando med anslutningssträngen för App Configuration Store. Du hittar anslutningssträngen under **Åtkomstnycklar** i Azure-portalen.

    Det här kommandot måste köras i samma katalog som *.csproj*-filen.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Vissa skal trunkerar anslutningssträngen om den inte omges av citattecken. Kontrollera att utdata `dotnet user-secrets` från kommandot visar hela anslutningssträngen. Om den inte gör det kör du kommandot igen och omsluter anslutningssträngen inom citationstecken.

    Secret Manager används bara för att testa webbappen lokalt. När appen distribueras till [Azure App Service](https://azure.microsoft.com/services/app-service/web), till exempel, använder du programinställningen **Anslutningssträngar** i App-tjänsten i stället för Secret Manager för att lagra anslutningssträngen.

    Få åtkomst till den här hemligheten med konfigurations-API:et. Ett kolon (:) fungerar i konfigurationsnamnet med konfigurations-API:et på alla plattformar som stöds. Se [Konfiguration efter miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Öppna *Program.cs*och lägg till en referens till .NET Core App Configuration Provider.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Uppdatera `CreateWebHostBuilder` metoden för att använda `config.AddAzureAppConfiguration()` appkonfiguration genom att anropa metoden.

    > [!IMPORTANT]
    > `CreateHostBuilder`ersätts `CreateWebHostBuilder` i .NET Core 3.0.  Välj rätt syntax baserat på din miljö.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

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

    ---

1. Navigera till * <app root>/Views/Home* och öppna *Index.cshtml*. Ersätt innehållet med följande kod:

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

1. Navigera till * <app root>/Views/Shared* och öppna *_Layout.cshtml*. Ersätt innehållet med följande kod:

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

1. Om du vill skapa appen med .NET Core CLI navigerar du till programmets rotkatalog och kör följande kommando i kommandoskalet:

    ```dotnetcli
    dotnet build
    ```

1. När versionen har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

1. Om du arbetar på din lokala dator använder `http://localhost:5000`du en webbläsare för att navigera till . Det här är standard-URL:en för webbappen lokalt.  

Om du arbetar i Azure Cloud Shell väljer du knappen *Förhandsgranska följt* av *Konfigurera*.  

![Leta reda på knappen Förhandsgranska](./media/quickstarts/cloud-shell-web-preview.png)

När du uppmanas att konfigurera porten för förhandsgranskning anger du '5000' och väljer *Öppna och bläddra*.  Webbsidan kommer att läsa "Data från Azure App Configuration."

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny App Configuration Store och använde den med en ASP.NET Core-webbapp via [appkonfigurationsleverantören](https://go.microsoft.com/fwlink/?linkid=2074664). Om du vill veta hur du konfigurerar din ASP.NET Core-appen för att dynamiskt uppdatera konfigurationsinställningarna fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-aspnet-core.md)
