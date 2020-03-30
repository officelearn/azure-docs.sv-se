---
title: Snabbstart för Azure App Configuration med .NET Framework | Microsoft Docs
description: En snabbstart för användning av Azure App Configuration med .NET Framework-appar
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 1c56088a2c51c50c7f9cf1ff1e790d580fdb08d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245403"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Snabbstart: Skapa en .NET Framework-app med Azure App-konfiguration

I den här snabbstarten införlivar du Azure App-konfiguration i en .NET Framework-baserad konsolapp för att centralisera lagring och hantering av programinställningar som är åtskilda från koden.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET-ramverket 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj**Skapa** > nyckelvärde för **konfigurationsutforskaren** > om du vill lägga till följande nyckel-värde-par:**Key-value**

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etikett** och **innehållstyp** tomma för tillfället.

7. Välj **Använd**.

## <a name="create-a-net-console-app"></a>Skapa en .NET-konsolapp

1. Starta Visual Studio och välj **Arkiv** > **Nytt** > **Projekt**.

1. I **Skapa ett nytt projekt**filtrerar du på **konsolprojekttypen** och klickar på Console App **(.NET Framework).** Välj **Nästa**.

1. Ange ett projektnamn **i Konfigurera det nya projektet.** Under **Ram**väljer du **.NET Framework 4.7.1** eller senare. Välj **Skapa**.

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. Sök och lägg till följande NuGet-paket på fliken **Bläddra** i. Om du inte hittar dem markerar du kryssrutan **Inkludera förhandsversion.**

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Uppdatera *filen App.config* i projektet enligt följande:

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

   Anslutningssträngen i App Configuration Store läss från miljövariabeln `ConnectionString`. Lägg `Environment` till konfigurationsverktyget före egenskapen `MyConfigStore` `configBuilders` i `appSettings` avsnittet.

1. Öppna *Program.cs*och uppdatera `Main` metoden för att använda `ConfigurationManager`Appkonfiguration genom att anropa .

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel med namnet **ConnectionString** till anslutningssträngen i appkonfigurationsarkivet. Om du använder kommandotolken i Windows kör du följande kommando:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Starta om Visual Studio så att ändringen kan börja gälla. Tryck på Ctrl + F5 för att skapa och köra konsolappen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny App Configuration Store och använde den med en .NET Framework-konsolapp. Värdet `AppSettings` `ConfigurationManager` för ändras inte när programmet har startats. Konfigurationsproviderbiblioteket för appkonfiguration .NET Standard kan dock också användas i en .NET Framework-app. Om du vill veta hur du aktiverar .NET Framework-appen för att dynamiskt uppdatera konfigurationsinställningarna fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-dotnet.md)
