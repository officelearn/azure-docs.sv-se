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
ms.openlocfilehash: cd4115aaeec15d14d48dcb71cbdc75212c6dc2db
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960689"
---
# <a name="quickstart-create-an-net-core-app-with-app-configuration"></a>Snabbstart: Skapa en .NET Core-app med App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Med den kan du enkelt lagra och hantera alla programinställningar på ett ställe som är separat från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en .NET Core-konsolapp.

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är dock ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför den här snabbstarten genom att installera [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Skapa en .NET Core-konsolapp

Du använder [.NET Core-kommandoradsgränssnittet (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt .NET Core-konsolapprojekt. Fördelen med att använda .NET Core CLI istället för Visual Studio är att det finns tillgängligt för både Windows-, macOS- och Linux-plattformar.

1. Skapa en ny mapp för ditt projekt.

2. Kör följande kommando i den nya mappen för att skapa ett nytt ASP.NET Core MVC-webbapp-projekt:

        dotnet new console

## <a name="connect-to-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Lägg till en referens till NuGet-paketet `Microsoft.Extensions.Configuration.AzureAppConfiguration` genom att köra följande kommando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Kör följande kommando för att återställa paket för projektet.

        dotnet restore

3. Öppna *Program.cs* och uppdatera metoden `Main` till att använda App Configuration genom att anropa metoden `builder.AddAzureAppConfiguration()`.

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

1. Ange en miljövariabel som heter **ConnectionString** till åtkomstnyckeln för ditt appkonfigurationsarkiv. Om du använder Windows-kommandotolken kör du följande kommando och startar om Kommandotolken så att ändringen börjar gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Kör följande kommando för att skapa konsolappen:

        dotnet build

3. När versionen har slutförts kör du följande kommando för att köra appen lokalt:

        dotnet run

    ![Snabbstart för appkörning](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett nytt appkonfigurationsarkiv och använt det med en .NET Core-konsolapp. Om du vill lära dig mer om att använda App Configuration fortsätter du till nästa självstudie som visar hur autentisering går till.

> [!div class="nextstepaction"]
> [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
