---
title: Snabbstart för Azure App Configuration med .NET Core | Microsoft Docs
description: En snabbstart för användning av Azure App Configuration med .NET Core-appar
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3f15b6bf5ff3cc1949794ebc1ee2a5f62158cede
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698630"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Snabbstart: Skapa en .NET Core-app med App-konfiguration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Du kan använda den för att enkelt lagra och hantera alla programinställningarna på ett ställe som är avgränsade från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en .NET Core-konsolapp.

Du kan använda valfri Kodredigerare för att utföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ tillgängligt på Windows, macOS och Linux-plattformar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill göra den här snabbstarten måste du installera den [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Skapa en .NET Core-konsolapp

Du använder den [.NET Core-kommandoradsgränssnittet (CLI)](https://docs.microsoft.com/dotnet/core/tools/) att skapa ett nytt projekt för .NET Core console app. Fördelen med att använda .NET Core CLI via Visual Studio är att den är tillgänglig i Windows, macOS och Linux-plattformar.

1. Skapa en ny mapp för ditt projekt.

2. Kör följande kommando för att skapa ett nytt ASP.NET Core MVC web app-projekt i den nya mappen:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till en appbutik för konfiguration

1. Lägg till en referens till den `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-paketet genom att köra följande kommando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-007830001

2. Kör följande kommando för att återställa paketen för ditt projekt:

        dotnet restore

3. Öppna *Program.cs*, och Lägg till en referens till en App Configuration .NET Core-konfigurationsprovider.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Uppdatera den `Main` metod du använder Appkonfiguration genom att anropa den `builder.AddAzureAppConfiguration()` metoden.

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

1. Ange en miljövariabel som heter **ConnectionString**, och ange att snabbtangent i din appbutik för konfiguration. Om du använder Windows-Kommandotolken kör du följande kommando och starta om Kommandotolken så att ändringen ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux, kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Kör följande kommando för att skapa konsolappen:

        dotnet build

3. När bygget har slutförts kör du följande kommando för att köra appen lokalt:

        dotnet run

    ![Snabbstart för appkörning](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en ny konfiguration appbutik och används med en .NET Core-konsolapp via den [App konfigurationsprovidern](https://go.microsoft.com/fwlink/?linkid=2074664). Om du vill veta mer om hur du använder Appkonfiguration kan du fortsätta till nästa självstudie som visar autentisering.

> [!div class="nextstepaction"]
> [Hanterade identiteter för integrering av Azure-resurser](./howto-integrate-azure-managed-service-identity.md)
