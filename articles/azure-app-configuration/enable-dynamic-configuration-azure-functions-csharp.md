---
title: Självstudiekurs för att använda dynamisk konfiguration av Azure App-konfiguration i en Azure Functions-app | Microsoft-dokument
description: I den här självstudien får du lära dig hur du dynamiskt uppdaterar konfigurationsdata för Azure Functions-appar
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74185452"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Självstudiekurs: Använda dynamisk konfiguration i en Azure Functions-app

Konfigurationsprovidern för appkonfiguration .NET Standard stöder cachelagring och uppdatering av konfiguration dynamiskt driven av programaktivitet. Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på Azure Functions-appen som introducerades i snabbstarten. Innan du fortsätter slutför du [Skapa en Azure-funktionsapp med Azure App-konfiguration](./quickstart-azure-functions-csharp.md) först.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera din Azure Functions-app för att uppdatera konfigurationen som svar på ändringar i ett App Configuration Store.
> * Injicera den senaste konfigurationen i dina Azure Functions-anrop.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med **Azure-utvecklingsarbetsbelastning**
- [Azure Functions-verktyg](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Slutför snabbstart [Skapa en Azure-funktionsapp med Azure App-konfiguration](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

1. Öppna *Function1.cs*. Förutom egenskapen `static` `Configuration`lägger du `static` `ConfigurationRefresher` till en ny egenskap `IConfigurationRefresher` för att behålla en singleton-instans av den som ska användas för att signalera konfigurationsuppdateringar under Functions-anrop senare.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Uppdatera konstruktorn och `ConfigureRefresh` använd metoden för att ange den inställning som ska uppdateras från App Configuration Store. En instans `IConfigurationRefresher` av hämtas med hjälp `GetRefresher` av metoden. Alternativt ändrar vi också fönstret för förfallodatum för konfigurationscachen till 1 minut från standard30 sekunder.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Uppdatera `Run` metoden och signalen för `Refresh` att uppdatera konfigurationen med hjälp av metoden i början av functions-anropet. Detta blir no-op om cachens tidsförfallotid inte nås. Ta `await` bort operatorn om du föredrar att konfigurationen uppdateras utan att blockera.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

1. Ange en miljövariabel med namnet **ConnectionString**och ange den till åtkomstnyckeln till appkonfigurationsarkivet. Om du använder kommandotolken i Windows kör du följande kommando och startar om kommandotolken så att ändringen kan börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Tryck på F5 för att testa funktionen. Om du uppmanas till det godkänner du begäran från Visual Studio om att hämta och installera **CLI-verktyg (Azure Functions Core).** Du kan också behöva aktivera ett brandväggsundantag så att verktygen kan hantera HTTP-begäranden.

3. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret i webbläsaren på den lokala GET-begäran som returneras av funktionen.

    ![Snabbstart för lokal funktionsstart](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser**och välj den App Configuration Store-instans som du skapade i snabbstarten.

6. Välj **Konfigurationsutforskaren**och uppdatera värdena för följande nyckel:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App-konfiguration - Uppdaterad |

7. Uppdatera webbläsaren några gånger. När den cachelagrade inställningen går ut efter en minut visas svaret för functions-anropet med uppdaterat värde.

    ![Uppdatering av snabbstartsfunktion lokal](./media/quickstarts/dotnet-core-function-refresh-local.png)

Exempelkoden som används i den här självstudien kan hämtas från [App Configuration GitHub repo](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat din Azure Functions-app för att dynamiskt uppdatera konfigurationsinställningarna från AppKonfiguration. Om du vill veta hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till appkonfiguration fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Hanterad identitetsintegrering](./howto-integrate-azure-managed-service-identity.md)
