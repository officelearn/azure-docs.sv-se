---
title: Självstudie för att använda Azure App dynamisk konfiguration dynamisk konfiguration i en .NET Core-app | Microsoft Docs
description: I den här självstudien får du lära dig att dynamiskt uppdatera konfigurations data för .NET Core-appar
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
ms.openlocfilehash: 3eee34f594cb23a8b64f6fd10837c9a641eda62d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075974"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Självstudier: Använd dynamisk konfiguration i en .NET Core-app

App Configuration .NET Core klient bibliotek stöder uppdatering av en uppsättning konfigurations inställningar på begäran utan att ett program startas om. Detta kan implementeras genom att först hämta en instans `IConfigurationRefresher` av från alternativen för konfigurationsprovidern och sedan anropa `Refresh` den instansen var som helst i din kod.

För att hålla inställningarna uppdaterade och undvika för många anrop till konfigurations arkivet används ett cacheminne för varje inställning. Tills det cachelagrade värdet för en inställning har gått ut uppdateras inte värdet, även om värdet har ändrats i konfigurations arkivet. Standard förfallo tiden för varje begäran är 30 sekunder, men den kan åsidosättas om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den app som introducerades i snabb starterna. Innan du fortsätter måste du först [skapa en .net Core-app med app-konfigurationen](./quickstart-dotnet-core-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ditt program för att uppdatera konfigurationen med ett konfigurations lager för appar på begäran.
> * Mata in den senaste konfigurationen i dina programs kontrollanter.

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här själv studie kursen installerar du [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

Öppna *program.cs* och uppdatera filen för att lägga till en referens till `System.Threading.Tasks` namn området, för att ange `AddAzureAppConfiguration` uppdaterings konfiguration i-metoden och aktivera manuell uppdatering med `Refresh` hjälp av-metoden.

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
}
```

`ConfigureRefresh` Metoden används för att ange inställningarna som används för att uppdatera konfigurations data med appens konfigurations Arkiv när en uppdatering aktive ras. En instans av `IConfigurationRefresher` kan hämtas genom att anropa `GetRefresher` metoden på de alternativ som ges `AddAzureAppConfiguration` `Refresh` till metoden och metoden på den här instansen kan användas för att utlösa en uppdatering var som helst i koden.
    
> [!NOTE]
> Standard-cachens förfallo tid för en konfigurations inställning är 30 sekunder, men kan åsidosättas genom att `SetCacheExpiration` anropa metoden på Options-initieraren som skickades som `ConfigureRefresh` ett argument till metoden.

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljö variabel med namnet **ConnectionString**och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Kör följande kommando för att skapa konsol programmet:

        dotnet build

1. När skapandet har slutförts kör du följande kommando för att köra appen lokalt:

        dotnet run

    ![Snabbstart av lokal app](./media/quickstarts/dotnet-core-app-run.png)

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **alla resurser**och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Välj **Configuration Explorer**och uppdatera värdena för följande nycklar:

    | Nyckel | Value |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App konfiguration – uppdaterad |

1. Tryck på RETUR-tangenten för att utlösa en uppdatering och skriv ut det uppdaterade värdet i kommando tolken eller PowerShell-fönstret.

    ![Snabbstart med uppdatering av lokal app](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Eftersom cachens förfallo tid var 10 sekunder med `SetCacheExpiration` metoden när konfigurationen för uppdaterings åtgärden angavs, uppdateras värdet för konfigurations inställningen endast om minst 10 sekunder har förflutit sedan den senaste uppdateringen för den inställningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill veta mer om hur du använder app-konfiguration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)
