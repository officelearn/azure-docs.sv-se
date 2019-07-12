---
title: Självstudie för att använda dynamisk konfiguration av Azure-konfiguration i en .NET Core-app | Microsoft Docs
description: I den här självstudien får du lära dig hur dynamiskt uppdatera konfigurationsinformationen för .NET Core-appar
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
ms.openlocfilehash: 1649fefda5073761d616fc48c602cab84d293ed0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799975"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Självstudier: Använd dynamisk konfiguration i en .NET Core-app

App Configuration .NET Core-klientbiblioteket har stöd för uppdatering av konfigurationsinställningar på begäran utan att orsaka ett program för att starta om. Detta kan implementeras första hämtar en instans av `IConfigurationRefresher` från alternativen för konfigurationstjänst och sedan anropa `Refresh` på instansen var som helst i din kod.

För att synkronisera de inställningar som uppdateras och undvika att för många anrop till konfigurationslagringen, används en cache för varje inställning. Tills det cachelagrade värdet för en inställning har upphört att gälla, uppdateras inte uppdateringsåtgärden värdet, även om värdet har ändrats i konfigurationslagringen. Standard förfallotid för varje begäran är 30 sekunder, men den kan åsidosättas om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den app som introducerades i snabbstarter. Innan du fortsätter Slutför [skapa en .NET Core-app med Appkonfiguration](./quickstart-dotnet-core-app.md) första.

Du kan använda valfri Kodredigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgänglig på Windows, macOS och Linux-plattformar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera programmet till att uppdatera konfigurationen med en appbutik konfiguration på begäran.
> * Mata in den senaste konfigurationen i ditt programs domänkontrollanter.

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här självstudien måste du installera den [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

Öppna *Program.cs* och uppdatera filen om du vill ange uppdatera konfigurationen i den `AddAzureAppConfiguration` metoden och utlösare manuell uppdatering med hjälp av den `Refresh` metoden.

```csharp
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        IConfigurationRefresher refresher = null;

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
```

Den `ConfigureRefresh` metod för att ange inställningar som används för att uppdatera konfigurationsdata med konfigurationslagringen appen när en uppdatering har utlösts. En instans av `IConfigurationRefresher` kan hämtas genom att anropa `GetRefresher` metoden på de alternativ som finns att `AddAzureAppConfiguration` metoden och `Refresh` metoden på den här instansen kan användas för att utlösa en uppdatering var som helst i din kod.
    
> [!NOTE]
> Förfallotid för cache för en konfigurationsinställning är 30 sekunder, men kan åsidosättas genom att anropa den `SetCacheExpiration` metoden på initieraren alternativ skickas som ett argument till den `ConfigureRefresh` metoden.

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel som heter **ConnectionString**, och ange att snabbtangent i din appbutik för konfiguration. Om du använder Windows-Kommandotolken kör du följande kommando och starta om Kommandotolken så att ändringen ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux, kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Kör följande kommando för att skapa konsolappen:

        dotnet build

1. När bygget har slutförts kör du följande kommando för att köra appen lokalt:

        dotnet run

    ![Snabbstart av lokal app](./media/quickstarts/dotnet-core-app-run.png)

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **alla resurser**, och välj den app configuration store-instansen som du skapade i snabbstarten.

1. Välj **Configuration Explorer**, och uppdatera värdena för följande nycklar:

    | Nyckel | Value |
    |---|---|
    | TestApp:Settings:Message | Data från Azure-App-konfiguration – uppdateras |

1. Tryck på RETUR-tangenten för att utlösa en uppdatering och skriva ut det uppdaterade värdet i fönstret Kommandotolken eller PowerShell.

    ![Snabbstart med uppdatering av lokal app](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Eftersom förfallotid för cache har angetts till 10 sekunder med hjälp av den `SetCacheExpiration` metoden när du anger konfigurationen för uppdateringsåtgärden, värdet för konfigurationsinställningen uppdateras endast om minst 10 sekunder har förflutit sedan den senaste uppdateringen för inställningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill veta mer om hur du använder Appkonfiguration, fortsätter du att Azure CLI-exempel.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)
