---
title: 'Självstudiekurs: Använda dynamisk konfiguration i en .NET Core-app'
titleSuffix: Azure App Configuration
description: I den här självstudien får du lära dig hur du dynamiskt uppdaterar konfigurationsdata för .NET Core-appar
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: afecc84748ae8ce85c07e3b482bd9b596bdca251
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433679"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Självstudiekurs: Använda dynamisk konfiguration i en .NET Core-app

App Configuration .NET Core-klientbiblioteket stöder uppdatering av en uppsättning konfigurationsinställningar på begäran utan att ett program startas om. Detta kan implementeras genom att `IConfigurationRefresher` först hämta en instans av `Refresh` från alternativen för konfigurationsleverantören och sedan anropa den instansen var som helst i koden.

För att hålla inställningarna uppdaterade och undvika för många anrop till konfigurationsarkivet används en cache för varje inställning. Tills cachelagrat värde för en inställning har upphört att gälla uppdateras inte värdet, inte ens när värdet har ändrats i konfigurationsarkivet. Standardtiden för förfallodatum för varje begäran är 30 sekunder, men den kan åsidosättas om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på appen som introducerades i snabbstarten. Innan du fortsätter slutför du [Skapa en .NET Core-app med appkonfiguration](./quickstart-dotnet-core-app.md) först.

Du kan använda vilken kodredigerare som helst för att göra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformarna.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera .NET Core-appen så att den uppdateras som svar på ändringar i ett App Configuration Store.
> * Förbruka den senaste konfigurationen i ditt program.

## <a name="prerequisites"></a>Krav

Om du vill göra den här självstudien installerar du [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

Öppna *Program.cs* och uppdatera filen för att `System.Threading.Tasks` lägga till en referens till `AddAzureAppConfiguration` namnområdet, ange uppdateringskonfiguration i metoden och för att utlösa manuell uppdatering med `Refresh` hjälp av metoden.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

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

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

Metoden `ConfigureRefresh` används för att ange de inställningar som används för att uppdatera konfigurationsdata med App Configuration Store när en uppdateringsåtgärd utlöses. En instans `IConfigurationRefresher` av kan hämtas genom att `GetRefresher` `AddAzureAppConfiguration` anropa metoden `Refresh` på de alternativ som anges till metoden, och metoden för den här instansen kan användas för att utlösa en uppdateringsåtgärd var som helst i koden.
    
> [!NOTE]
> Standardtiden för förfallotiden för cache för en konfigurationsinställning är `SetCacheExpiration` 30 sekunder, men kan åsidosättas genom att anropa metoden på alternativen som skickas som ett argument till `ConfigureRefresh` metoden.

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel med namnet **ConnectionString**och ange den till åtkomstnyckeln till appkonfigurationsarkivet. Om du använder kommandotolken i Windows kör du följande kommando och startar om kommandotolken så att ändringen kan börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Kör följande kommando för att skapa konsolappen:

        dotnet build

1. När versionen har slutförts kör du följande kommando för att köra appen lokalt:

        dotnet run

    ![Snabbstart av lokal app](./media/quickstarts/dotnet-core-app-run.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser**och välj den App Configuration Store-instans som du skapade i snabbstarten.

1. Välj **Konfigurationsutforskaren**och uppdatera värdena för följande nycklar:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App-konfiguration - Uppdaterad |

1. Tryck på Retur för att utlösa en uppdatering och skriva ut det uppdaterade värdet i kommandotolken eller PowerShell-fönstret.

    ![Snabbstart med uppdatering av lokal app](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Eftersom cachetiden förfallotid angavs till `SetCacheExpiration` 10 sekunder med hjälp av metoden när du anger konfigurationen för uppdateringsåtgärden, uppdateras värdet för konfigurationsinställningen endast om minst 10 sekunder har förflutit sedan den senaste uppdateringen för den inställningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat .NET Core-appen för att dynamiskt uppdatera konfigurationsinställningarna från Appkonfiguration. Om du vill veta hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till appkonfiguration fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Hanterad identitetsintegrering](./howto-integrate-azure-managed-service-identity.md)
