---
title: Snabbstart för Azure App Configuration med .NET Framework | Microsoft Docs
description: I den här artikeln skapar du en .NET Framework-app med Azure App konfiguration för att centralisera lagring och hantering av program inställningar separat från din kod.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: 4edf48b73ee4d50679cbec377ed47749d2c94776
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91569609"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Snabb start: skapa en .NET Framework-app med Azure App konfiguration

I den här snabb starten inkluderar du Azure App konfiguration i en .NET Framework-baserad konsolsession för att centralisera lagring och hantering av program inställningar separat från din kod.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **konfigurations Utforskaren**  >  **skapa**  >  **nyckel-värde** om du vill lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

7. Välj **Tillämpa**.

## <a name="create-a-net-console-app"></a>Skapa en .NET-konsolapp

1. Starta Visual Studio och välj **fil**  >  **nytt**  >  **projekt**.

1. I **skapa ett nytt projekt**filtrerar du på projekt typen **konsol** och klickar på **konsol program (.NET Framework)**. Välj **Nästa**.

1. Ange ett projekt namn i **Konfigurera ditt nya projekt**. Under **ramverk**väljer du **.NET Framework 4.7.1** eller högre. Välj **Skapa**.

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. På fliken **Bläddra** söker du och lägger till följande NuGet-paket i projektet.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Uppdatera *App.config* -filen för projektet på följande sätt:

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

   Anslutnings strängen för appens konfigurations lager läses från miljövariabeln `ConnectionString` . Lägg till `Environment` konfigurations verktyget före `MyConfigStore` i `configBuilders` egenskapen för `appSettings` avsnittet.

1. Öppna *program.cs*och uppdatera `Main` metoden för att använda app-konfiguration genom att anropa `ConfigurationManager` .

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Uppdatera **App.config** -filen genom `${ConnectionString}` att ersätta med den faktiska anslutnings strängen till program konfigurations instansen. Du hittar den på fliken **åtkomst nycklar** i program konfigurations resursen i Azure-portalen.

1. Tryck på Ctrl + F5 för att skapa och köra-konsol programmet.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett nytt konfigurations Arkiv för appar och använt det med en .NET Framework-konsol-app. Värdet kan `AppSettings` `ConfigurationManager` inte ändras efter att programmet har startats. Appens konfiguration för .NET-standardkonfigurations leverantörs bibliotek, kan dock också användas i en .NET Framework app. Fortsätt till nästa självstudie om du vill lära dig hur du aktiverar .NET Framework-appen att dynamiskt uppdatera konfigurations inställningar.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-dotnet.md)
