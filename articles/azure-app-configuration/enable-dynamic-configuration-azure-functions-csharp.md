---
title: Självstudie för att använda Azure App dynamisk konfiguration dynamisk konfiguration i en Azure Functions app | Microsoft Docs
description: I den här självstudien får du lära dig att dynamiskt uppdatera konfigurations data för Azure Functions appar
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
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185452"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Självstudie: Använd dynamisk konfiguration i en Azure Functions app

Appens konfiguration .NET-standardprovidern för konfiguration stöder cachelagring och uppdatering av konfigurationen dynamiskt genom program aktivitet. Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den Azure Functions app som introducerades i snabb starterna. Innan du fortsätter måste du [skapa en Azure Functions-app med Azure App-konfigurationen](./quickstart-azure-functions-csharp.md) först.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera din Azure Functions-app för att uppdatera konfigurationen som svar på ändringar i ett konfigurations lager för appar.
> * Mata in den senaste konfigurationen till dina Azure Functions-anrop.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med arbets belastningen **Azure Development**
- [Azure Functions verktyg](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Slutför snabb start [skapa en Azure Functions-app med Azure App konfiguration](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

1. Öppna *Function1.cs*. Förutom `static` egenskap `Configuration`lägger du till en ny `static` egenskap `ConfigurationRefresher` för att hålla en singleton-instans av `IConfigurationRefresher` som ska användas för att signalera konfigurations uppdateringar under funktions anrop senare.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Uppdatera konstruktorn och Använd `ConfigureRefresh`-metoden för att ange inställningen som ska uppdateras från App Configuration Store. En instans av `IConfigurationRefresher` hämtas med hjälp av `GetRefresher`-metoden. Om du vill kan vi också ändra tids perioden för konfigurations-cachens förfallo tid till 1 minut från standard 30 sekunder.

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

3. Uppdatera `Run`-metoden och signalen för att uppdatera konfigurationen med hjälp av metoden `Refresh` i början av funktions anropet. Detta är ingen-op om perioden för förfallo tid för cache inte har uppnåtts. Ta bort `await` operatören om du vill att konfigurationen ska uppdateras utan att blockeras.

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

1. Ange en miljö variabel med namnet **ConnectionString**och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Tryck på F5 för att testa funktionen. Om du får en uppmaning accepterar du begäran från Visual Studio för att ladda ned och installera **Azure Functions Core (CLI)** -verktyg. Du kan också behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-begäranden.

3. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret i webbläsaren till den lokala GET-begäran som returnerades av funktionen.

    ![Snabbstart för lokal funktionsstart](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser**och välj den instans av app Configuration Store som du skapade i snabb starten.

6. Välj **Configuration Explorer**och uppdatera värdena för följande nyckel:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App konfiguration – uppdaterad |

7. Uppdatera webbläsaren några gånger. När den cachelagrade inställningen upphör att gälla efter en minut, visar sidan svaret på funktions anropet med det uppdaterade värdet.

    ![Snabb starts funktion uppdatera lokal](./media/quickstarts/dotnet-core-function-refresh-local.png)

Exempel koden som används i den här självstudien kan hämtas från [app Configuration GitHub lagrings platsen](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat Azure Functions-appen för dynamisk uppdatering av konfigurations inställningar från App-konfigurationen. Fortsätt till nästa självstudie om du vill lära dig hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
