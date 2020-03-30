---
title: Snabbstart för att lägga till funktionsflaggor i .NET Framework-appar | Microsoft Dokument | Microsoft-dokument
description: En snabbstart för att lägga till funktionsflaggor i .NET Framework-appar och hantera dem i Azure App-konfiguration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 5ea9749c07aadc7037e753160e9b053992bebae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619327"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Snabbstart: Lägga till funktionsflaggor i en .NET Framework-app

I den här snabbstarten införlivar du Azure App-konfiguration i en .NET Framework-app för att skapa en implementering från slutna till. Du kan använda tjänsten Appkonfiguration för att centralt lagra alla dina funktionsflaggor och styra deras tillstånd. 

.NET-funktionshanteringsbiblioteken utökar ramverket med omfattande stöd för funktionsflagga. Dessa bibliotek är byggda ovanpå .NET-konfigurationssystemet. De integreras sömlöst med App-konfiguration via .NET-konfigurationsprovidern.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET-ramverket 4,8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Funktionshanteraren** > **+Lägg** till `Beta`om du vill lägga till en funktionsflagga med namnet .

    > [!div class="mx-imgBorder"]
    > ![Aktivera funktionsflagga med namnet Beta](media/add-beta-feature-flag.png)

    Lämna `label` odefinierad för tillfället.

## <a name="create-a-net-console-app"></a>Skapa en .NET-konsolapp

1. Starta Visual Studio och välj **Arkiv** > **Nytt** > **Projekt**.

1. I **Skapa ett nytt projekt**filtrerar du på **konsolprojekttypen** och klickar på Console App **(.NET Framework).** Klicka på **Nästa**.

1. Ange ett projektnamn **i Konfigurera det nya projektet.** Under **Ram**väljer du **.NET Framework 4.8** eller senare. Klicka på **Skapa**.

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. Sök och lägg till följande NuGet-paket på fliken **Bläddra** i. Om du inte hittar dem markerar du kryssrutan **Inkludera förhandsversion.**

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Öppna *Program.cs* och lägg till följande satser:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Uppdatera `Main` metoden för att ansluta till `UseFeatureFlags` appkonfiguration och ange alternativet så att funktionsflaggor hämtas. Visa sedan ett `Beta` meddelande om funktionsflaggan är aktiverad.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel med namnet **ConnectionString** till anslutningssträngen i appkonfigurationsarkivet. Om du använder kommandotolken i Windows kör du följande kommando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Starta om Visual Studio så att ändringen kan börja gälla. 

1. Tryck på Ctrl + F5 för att skapa och köra konsolappen.

    ![App med funktionsflagga aktiverad](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en funktionsflagga i Appkonfiguration och använde den med en .NET Framework-konsolapp. Om du vill veta hur du dynamiskt uppdaterar funktionsflaggor och andra konfigurationsvärden utan att starta om programmet fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-dotnet.md)
