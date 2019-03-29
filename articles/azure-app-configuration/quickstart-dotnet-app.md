---
title: Snabbstart för Azure App Configuration med .NET Framework | Microsoft Docs
description: En snabbstart för användning av Azure App Configuration med .NET Framework-appar
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b4cb571653cbe69939a1cbdc92338663e4e7125f
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576080"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Snabbstart: Skapa en .NET Framework-app med Azure App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Du kan använda den för att enkelt lagra och hantera alla programinställningarna på ett ställe som är avgränsade från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en .NET Framework-baserad Windows-skrivbordskonsolapp.

![Snabbstart för fullständig lokal](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här snabbstarten, installera [Visual Studio 2017](https://visualstudio.microsoft.com/vs) och [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) eller senare om du inte redan har gjort.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Skapa en .NET-konsolapp

1. Starta Visual Studio och välj **filen** > **New** > **projekt**.

2. I **nytt projekt**väljer **installerad** > **Visual C#**   >  **Windows Desktop**. Välj **Konsolapp (.NET Framework)**, och ange ett namn för ditt projekt. Välj **.NET Framework 4.7.1** eller dig och välj **OK**.

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till en appbutik för konfiguration

1. Högerklicka på projektet och välj **hantera NuGet-paket**. På den **Bläddra** fliken, söka och Lägg till följande NuGet-paketen i projektet. Om du inte kan hitta dem, väljer du den **inkludera förhandsversion** markerar du kryssrutan.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Uppdatera den *App.config* filen i ditt projekt på följande sätt:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Anslutningssträngen för din app configuration store har lästs från miljövariabeln `ConnectionString`. Lägg till den `Environment` configuration builder innan den `MyConfigStore` i den `configBuilders` egenskapen för den `appSettings` avsnittet.

3. Öppna *Program.cs*, och uppdatera den `Main` metod du använder Appkonfiguration genom att anropa `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel som heter **ConnectionString** till anslutningssträngen för ditt appkonfigurationsarkiv. Om du använder Windows-Kommandotolken kör du följande kommando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Starta om Visual Studio så att ändringen ska börja gälla. Tryck på Ctrl + F5 för att skapa och köra konsolappen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten du skapade en ny konfiguration appbutik och använder den med en .NET Framework-konsolapp. Om du vill veta mer om hur du använder Appkonfiguration kan du fortsätta till nästa självstudie som visar autentisering.

> [!div class="nextstepaction"]
> [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
