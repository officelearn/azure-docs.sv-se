---
title: Snabbstart för Azure App Configuration med .NET Core | Microsoft Docs
description: En snabbstart för användning av Azure App Configuration med .NET Core-appar
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 420d9b48013f5f6debe588667fe1cc0390517e66
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245386"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Snabbstart: Skapa en .NET Core-app med appkonfiguration

I den här snabbstarten införlivar du Azure App-konfiguration i en .NET Core-konsolapp för att centralisera lagring och hantering av programinställningar som är åtskilda från koden.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download) - även tillgängligt i [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj**Skapa** > nyckelvärde för **konfigurationsutforskaren** > om du vill lägga till följande nyckel-värde-par:**Key-value**

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etikett** och **innehållstyp** tomma för tillfället.

7. Välj **Använd**.

## <a name="create-a-net-core-console-app"></a>Skapa en .NET Core-konsolapp

Du kan använda [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt .NET Core-konsolappprojekt. Fördelen med att använda .NET Core CLI över Visual Studio är att den är tillgänglig på Windows-, macOS- och Linux-plattformarna.  Du kan också använda de förinstallerade verktygen som finns i [Azure Cloud Shell](https://shell.azure.com).

1. Skapa en ny mapp för ditt projekt.

2. I den nya mappen kör du följande kommando för att skapa ett nytt ASP.NET Core-konsolappprojekt:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Lägg till en `Microsoft.Extensions.Configuration.AzureAppConfiguration` referens till NuGet-paketet genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Kör följande kommando för att återställa paket för projektet:

    ```dotnetcli
    dotnet restore
    ```

3. Öppna *Program.cs*och lägg till en referens till .NET Core App Configuration Provider.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Uppdatera `Main` metoden för att använda `builder.AddAzureAppConfiguration()` appkonfiguration genom att anropa metoden.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel med namnet **ConnectionString**och ange den till åtkomstnyckeln till appkonfigurationsarkivet. Kör följande kommando på kommandoraden:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

    Starta om kommandotolken så att ändringen kan börja gälla. Skriv ut värdet för miljövariabeln för att verifiera att den är korrekt inställd.

2. Kör följande kommando för att skapa konsolappen:

    ```dotnetcli
    dotnet build
    ```

3. När versionen har slutförts kör du följande kommando för att köra appen lokalt:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nytt App Configuration Store och använde det med en .NET Core-konsolapp via [appkonfigurationsleverantören](https://go.microsoft.com/fwlink/?linkid=2074664). Om du vill veta hur du konfigurerar .NET Core-appen för att dynamiskt uppdatera konfigurationsinställningarna fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-dotnet-core.md)
