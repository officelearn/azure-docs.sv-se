---
title: Snabb start för att lägga till funktions flaggor i Azure Functions | Microsoft Docs
description: I den här snabb starten ska du använda Azure Functions med funktions flaggor från Azure App konfiguration och testa funktionen lokalt.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: f57163b3d17877f7f8c66fa57fc8a0736c6affb7
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767703"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Snabb start: Lägg till funktions flaggor i en Azure Functions app

I den här snabb starten skapar du en implementering av funktions hantering i en Azure Functions-app med hjälp av Azure App konfiguration. Du kommer att använda app Configuration service för att centralt lagra alla dina funktions flaggor och kontrol lera deras tillstånd. 

Biblioteken för .NET-funktions hantering utökar ramverket med stöd för funktions flagga. Dessa bibliotek skapas ovanpå .NET-konfigurations systemet. De integreras med app-konfiguration via dess .NET-Konfigurationsprovider.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med arbets belastningen **Azure Development** .
- [Azure Functions verktyg](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Välj **funktions hanteraren**  >  **+ Lägg** till för att lägga till en funktions flagga som kallas `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Aktivera funktions flagga med namnet beta](media/add-beta-feature-flag.png)

    Lämna `label` och gör `Description` odefinierat för tillfället.

8. Välj **Använd** för att spara den nya funktions flaggan.

## <a name="create-a-functions-app"></a>Skapa en Functions-app

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. På fliken **Bläddra** söker du och lägger till följande NuGet-paket i projektet. Kontrol lera `Microsoft.Extensions.DependencyInjection` att du har den senaste säkra versionen. 

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration
    Microsoft.FeatureManagement
    ```


1. Öppna *Function1.cs*och Lägg till namn rymderna för dessa paket.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.DependencyInjection;
    ```

1. Lägg till den `Function1` statiska konstruktorn nedan för att starta Azure App konfigurationsprovidern. Lägg sedan till två `static` medlemmar, ett fält med namnet `ServiceProvider` för att skapa en singleton-instans av `ServiceProvider` och en egenskap under `Function1` namn `FeatureManager` för att skapa en singleton-instans av `IFeatureManager` . Anslut sedan till appens konfiguration i `Function1` genom att anropa `AddAzureAppConfiguration()` . Den här processen läser in konfigurationen när programmet startas. Samma konfigurations instans kommer att användas för alla funktions anrop senare. 

    ```csharp
        // Implements IDisposable, cached for life time of function
        private static ServiceProvider ServiceProvider; 

        static Function1()
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            var services = new ServiceCollection();                                                                             
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            ServiceProvider = services.BuildServiceProvider(); 
        }

        private static IFeatureManager FeatureManager => ServiceProvider.GetRequiredService<IFeatureManager>();
    ```

1. Uppdatera `Run` metoden för att ändra värdet för det visade meddelandet beroende på funktions flaggans tillstånd.

    ```csharp
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
                [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
                ILogger log)
            {
                string message = await FeatureManager.IsEnabledAsync("Beta")
                     ? "The Feature Flag 'Beta' is turned ON"
                     : "The Feature Flag 'Beta' is turned OFF";
                
                return (ActionResult)new OkObjectResult(message); 
            }
    ```

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

1. Ange en miljö variabel med namnet **ConnectionString**, där värdet är den åtkomst nyckel som du hämtade tidigare i konfigurations arkivet för appen under **åtkomst nycklar**. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Tryck på F5 för att testa funktionen. Om du uppmanas att göra det accepterar du begäran från Visual Studio för att ladda ned och installera **Azure Functions Core-verktyg (CLI)** . Du kan också behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-begäranden.

1. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

1. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret som indikerar att funktions flaggan `Beta` är inaktive rad. 

    ![Funktions flagga för snabb starts funktion inaktiverat](./media/quickstarts/functions-launch-ff-disabled.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser**och välj den instans av konfigurations Arkiv för app som du skapade.

1. Välj **funktions hanteraren**och ändra statusen för **beta** nyckeln till **på**.

1. Återgå till kommando tolken och Avbryt processen som körs genom att trycka på `Ctrl-C` .  Starta om programmet genom att trycka på F5. 

1. Kopiera URL: en för din funktion från Azure Functions runtime-utdata med samma process som i steg 3. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Webbläsarens svar bör ha ändrats för att visa att funktions flaggan `Beta` är aktive rad, som visas på bilden nedan.
 
    ![Funktions flagga för snabb starts funktion aktive rad](./media/quickstarts/functions-launch-ff-enabled.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en funktions flagga och använde den med en Azure Functions app via [appens Konfigurationsprovider](https://go.microsoft.com/fwlink/?linkid=2074664).

- Läs mer om [funktions hantering](./concept-feature-management.md).
- [Hantera funktions flaggor](./manage-feature-flags.md).
- [Använd dynamisk konfiguration i en Azure Functions app](./enable-dynamic-configuration-azure-functions-csharp.md)
