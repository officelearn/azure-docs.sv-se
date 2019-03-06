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
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962170"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Snabbstart: Skapa en .NET Framework-app med Azure App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Med den kan du enkelt lagra och hantera alla programinställningar på ett ställe som är separat från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en .NET Framework-baserad Windows-skrivbordskonsolapp.

![Quickstart Complete local](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten installerar du [Visual Studio 2017](https://visualstudio.microsoft.com/vs) och [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) eller senare om du inte redan har gjort det.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Skapa en .NET-konsolapp

1. Starta Visual Studio och välj **Arkiv** > **Nytt** > **Projekt**.

2. I dialogrutan **Nytt projekt** väljer du **Installerat**, expanderar **Visual C#** > **Windows Desktop**, väljer **Konsolapp (.NET Framework)**, skriver ett **Namn** för projektet, väljer **.NET Framework 4.7.1** eller högre och klickar på **OK**.

## <a name="connect-to-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Högerklicka på projektet och välj **Hantera NuGet-paket...**. På fliken **Bläddra** söker du och lägger till följande NuGet-paket i projektet (markera rutan **Ta med förhandsversion** om de inte visas).
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Uppdatera filen *App.config* i projektet på följande sätt:

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

   Observera att eftersom vi kommer att läsa anslutningssträngen för ditt appkonfigurationsarkiv från miljövariabeln `ConnectionString` är det viktigt att lägga till konfigurationsbyggaren `Environment` före `MyConfigStore` i egenskapen `configBuilders` i avsnittet `appSettings`.

3. Öppna *Program.cs* och uppdatera metoden `Main` till att använda App Configuration genom att anropa `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel som heter **ConnectionString** till anslutningssträngen för ditt appkonfigurationsarkiv. Om du använder Windows-kommandotolken kör du följande kommando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Starta om Visual Studio så att ändringen börjar gälla, och tryck sedan på **Ctrl + F5** på tangentbordet för att skapa och köra konsolappen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett nytt appkonfigurationsarkiv och använt det med en .NET Framework-konsolapp. Om du vill lära dig mer om att använda App Configuration fortsätter du till nästa självstudie som visar hur autentisering går till.

> [!div class="nextstepaction"]
> [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
