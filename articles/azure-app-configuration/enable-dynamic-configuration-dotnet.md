---
title: Självstudie för att använda Azure App dynamisk konfiguration dynamisk konfiguration i en .NET Framework app | Microsoft Docs
description: I den här självstudien får du lära dig att dynamiskt uppdatera konfigurations data för .NET Framework appar
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7e28cdacce8eac4774683013ae1c30ca34ebfaad
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821713"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Självstudie: Använd dynamisk konfiguration i en .NET Framework app

App Configuration .NET-klient biblioteket stöder uppdatering av en uppsättning konfigurations inställningar på begäran utan att ett program startas om. Detta kan implementeras genom att först hämta en instans av `IConfigurationRefresher` från alternativen för konfigurationsprovidern och sedan anropa `Refresh` på den instansen var som helst i din kod.

För att hålla inställningarna uppdaterade och undvika för många anrop till konfigurations arkivet används ett cacheminne för varje inställning. Tills det cachelagrade värdet för en inställning har gått ut uppdateras inte värdet, även om värdet har ändrats i konfigurations arkivet. Standard förfallo tiden för varje begäran är 30 sekunder, men den kan åsidosättas om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den app som introducerades i snabb starterna. Innan du fortsätter måste du först [skapa en .NET Framework-app med app-konfigurationen](./quickstart-dotnet-app.md) .

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ditt program för att uppdatera konfigurationen med ett konfigurations lager för appar på begäran.
> * Mata in den senaste konfigurationen i dina programs kontrollanter.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 eller senare](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Configuration Explorer** >  **+ skapa** för att lägga till följande nyckel/värde-par:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

## <a name="create-a-net-console-app"></a>Skapa en .NET-konsolapp

1. Starta Visual Studio och välj **fil** > **nytt** > -**projekt**.

1. I **skapa ett nytt projekt**filtrerar du på projekt typen **konsol** och klickar på **konsol program (.NET Framework)** . Klicka på **Next**.

1. Ange ett projekt namn i **Konfigurera ditt nya projekt**. Under **ramverk**väljer du **.NET Framework 4.7.1** eller högre. Klicka på **Skapa**.

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration
1. Högerklicka på projektet och välj **Hantera NuGet-paket**. På fliken **Bläddra** söker du och lägger till *Microsoft. Extensions. Configuration. AzureAppConfiguration* NuGet-paketet i projektet. Om du inte hittar det markerar du kryss rutan **Inkludera för hands version** .

1. Öppna *program.cs*och Lägg till en referens till .net Core app Configuration-providern.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Lägg till två variabler för att lagra konfigurations relaterade objekt.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Uppdatera `Main`-metoden för att ansluta till app-konfigurationen med de angivna uppdaterings alternativen.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    Metoden `ConfigureRefresh` används för att ange inställningarna som används för att uppdatera konfigurations data med konfigurations arkivet för appar när en uppdatering aktive ras. En instans av `IConfigurationRefresher` kan hämtas genom att anropa `GetRefresher` metod på de alternativ som ges till `AddAzureAppConfiguration` metod och `Refresh`-metoden på den här instansen kan användas för att utlösa en uppdatering var som helst i koden.

    > [!NOTE]
    > Standard-cachens förfallo tid för en konfigurations inställning är 30 sekunder, men kan åsidosättas genom att anropa metoden `SetCacheExpiration` på Options-initieraren som skickas som ett argument till `ConfigureRefresh`-metoden.

1. Lägg till en metod som heter `PrintMessage()` som utlöser en manuell uppdatering av konfigurations data från App-konfigurationen.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljö variabel med namnet **ConnectionString**och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Starta om Visual Studio om du vill att ändringen ska börja gälla. 

1. Tryck på Ctrl + F5 för att skapa och köra-konsol programmet.

    ![App-starta lokalt](./media/dotnet-app-run.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser**och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Välj **Configuration Explorer**och uppdatera värdena för följande nycklar:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App konfiguration – uppdaterad |

1. Gå tillbaka till det program som körs, tryck på RETUR-tangenten för att utlösa en uppdatering och skriv ut det uppdaterade värdet i kommando tolken eller PowerShell-fönstret.

    ![Uppdatera lokala appar](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Eftersom cachens förfallo tid var 10 sekunder med metoden `SetCacheExpiration` när du angav konfigurationen för uppdaterings åtgärden, uppdateras värdet för konfigurations inställningen bara om minst 10 sekunder har förflutit sedan den senaste uppdateringen för den inställningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Fortsätt till nästa självstudie om du vill lära dig hur du lägger till en Azure-hanterad tjänst identitet som effektiviserar åtkomst till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
