---
title: '.NET Framework-självstudiekurs: dynamisk konfiguration i Azure App-konfiguration'
description: I den här självstudien får du lära dig hur du dynamiskt uppdaterar konfigurationsdata för .NET Framework-appar med Azure App-konfiguration.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7780bdbc92868f62e8d066d171b2a04fe06a981d
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80245811"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Självstudiekurs: Använda dynamisk konfiguration i en .NET Framework-app

App Configuration .NET-klientbiblioteket stöder uppdatering av en uppsättning konfigurationsinställningar på begäran utan att ett program startas om. Detta kan implementeras genom att `IConfigurationRefresher` först hämta en instans av `Refresh` från alternativen för konfigurationsleverantören och sedan anropa den instansen var som helst i koden.

För att hålla inställningarna uppdaterade och undvika för många anrop till konfigurationsarkivet används en cache för varje inställning. Tills cachelagrat värde för en inställning har upphört att gälla uppdateras inte värdet, inte ens när värdet har ändrats i konfigurationsarkivet. Standardtiden för förfallodatum för varje begäran är 30 sekunder, men den kan åsidosättas om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på appen som introducerades i snabbstarten. Innan du fortsätter slutför du [Skapa en .NET Framework-app med appkonfiguration](./quickstart-dotnet-app.md) först.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera .NET Framework-appen så att den uppdateras som svar på ändringar i ett App Configuration Store.
> * Injicera den senaste konfigurationen i ditt program.
## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 eller senare](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Konfigurationsutforskaren** > **+ Skapa** > **nyckelvärde** om du vill lägga till följande nyckel-värde-par:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etikett** och **innehållstyp** tomma för tillfället.

7. Välj **Använd**.

## <a name="create-a-net-framework-console-app"></a>Skapa en KONSOLAPP för .NET Framework

1. Starta Visual Studio och välj **Arkiv** > **Nytt** > **Projekt**.

1. I **Skapa ett nytt projekt**filtrerar du på **konsolprojekttypen** och klickar på Console App **(.NET Framework).** Klicka på **Nästa**.

1. Ange ett projektnamn **i Konfigurera det nya projektet.** Under **Ram**väljer du **.NET Framework 4.7.1** eller senare. Klicka på **Skapa**.

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration
1. Högerklicka på projektet och välj **Hantera NuGet-paket**. Sök och lägg till *paketet Microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet i projektet på fliken **Bläddra.** Om du inte hittar den markerar du kryssrutan **Inkludera förhandsversion.**

1. Öppna *Program.cs*och lägg till en referens till .NET Core App Configuration Provider.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Lägg till två variabler för att lagra konfigurationsrelaterade objekt.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Uppdatera `Main` metoden för att ansluta till appkonfiguration med de angivna uppdateringsalternativen.

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
    Metoden `ConfigureRefresh` används för att ange de inställningar som används för att uppdatera konfigurationsdata med App Configuration Store när en uppdateringsåtgärd utlöses. En instans `IConfigurationRefresher` av kan hämtas genom att `GetRefresher` `AddAzureAppConfiguration` anropa metoden `Refresh` på de alternativ som anges till metoden, och metoden på den här instansen kan användas för att utlösa en uppdateringsåtgärd var som helst i koden.

    > [!NOTE]
    > Standardtiden för förfallotiden för cache för en konfigurationsinställning är `SetCacheExpiration` 30 sekunder, men kan åsidosättas genom att anropa metoden på alternativen som skickas som ett argument till `ConfigureRefresh` metoden.

1. Lägg till `PrintMessage()` en metod som kallas som utlöser en manuell uppdatering av konfigurationsdata från appkonfiguration.

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

1. Ange en miljövariabel med namnet **ConnectionString**och ange den till åtkomstnyckeln till appkonfigurationsarkivet. Om du använder kommandotolken i Windows kör du följande kommando och startar om kommandotolken så att ändringen kan börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Starta om Visual Studio så att ändringen kan börja gälla. 

1. Tryck på Ctrl + F5 för att skapa och köra konsolappen.

    ![Starta app lokalt](./media/dotnet-app-run.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser**och välj den App Configuration Store-instans som du skapade i snabbstarten.

1. Välj **Konfigurationsutforskaren**och uppdatera värdena för följande nycklar:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App-konfiguration - Uppdaterad |

1. Tillbaka i programmet som körs trycker du på Retur för att utlösa en uppdatering och skriver ut det uppdaterade värdet i kommandotolken eller PowerShell-fönstret.

    ![Uppdatera appen lokalt](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Eftersom cachetiden förfallotid angavs till `SetCacheExpiration` 10 sekunder med hjälp av metoden när du anger konfigurationen för uppdateringsåtgärden, uppdateras värdet för konfigurationsinställningen endast om minst 10 sekunder har förflutit sedan den senaste uppdateringen för den inställningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat .NET Framework-appen för att dynamiskt uppdatera konfigurationsinställningarna från Appkonfiguration. Om du vill veta hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till appkonfiguration fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Hanterad identitetsintegrering](./howto-integrate-azure-managed-service-identity.md)
