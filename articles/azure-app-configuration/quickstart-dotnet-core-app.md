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
ms.openlocfilehash: 986bda4ab95985820e61c93bbe82431fb50c983b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185015"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Snabb start: skapa en .NET Core-app med app-konfiguration

I den här snabb starten inkluderar du Azure App konfiguration i en .NET Core-konsol för att centralisera lagring och hantering av program inställningar separat från din kod.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **konfigurations utforskaren** >  **+ skapa** för att lägga till följande nyckel/värde-par:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

## <a name="create-a-net-core-console-app"></a>Skapa en .NET Core-konsolapp

Du använder [.net Core kommando rads gränssnitt (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt .net Core Console-projekt. Fördelen med att använda .NET Core CLI i Visual Studio är att den är tillgänglig på Windows-, macOS-och Linux-plattformarna.

1. Skapa en ny mapp för ditt projekt.

2. I den nya mappen kör du följande kommando för att skapa ett nytt ASP.NET Core Console-projekt:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Lägg till en referens till `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-paketet genom att köra följande kommando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 2.0.0-preview-009470001-1371

2. Kör följande kommando för att återställa paket för ditt projekt:

        dotnet restore

3. Öppna *program.cs*och Lägg till en referens till .net Core app Configuration-providern.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Uppdatera `Main`-metoden för att använda app-konfiguration genom att anropa `builder.AddAzureAppConfiguration()`-metoden.

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

1. Ange en miljö variabel med namnet **ConnectionString**och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Kör följande kommando för att skapa konsol programmet:

        dotnet build

3. När skapandet har slutförts kör du följande kommando för att köra appen lokalt:

        dotnet run

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nytt konfigurations Arkiv för appar och använde det med en .NET Core-konsol-app via [appens Konfigurationsprovider](https://go.microsoft.com/fwlink/?linkid=2074664). Fortsätt till nästa självstudie om du vill lära dig hur du konfigurerar din .NET Core-app så att konfigurations inställningarna uppdateras dynamiskt.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-dotnet-core.md)
