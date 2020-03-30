---
title: Snabbstart för Azure App Configuration med Azure Functions | Microsoft Docs
description: En snabbstart för användning av Azure App Configuration med Azure Functions.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 2f6efdad7ab0685e58d2edd73bc36b758e8dbae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245505"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Snabbstart: Skapa en Azure Functions-app med Azure App-konfiguration

I den här snabbstarten införlivar du Azure App Configuration-tjänsten i en Azure Functions-app för att centralisera lagring och hantering av alla dina programinställningar som är åtskilda från din kod.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med **Azure-utvecklingsarbetsbelastningen.**
- [Azure Functions-verktyg](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Konfigurationsutforskaren** > **+ Skapa** > **nyckelvärde** om du vill lägga till följande nyckel-värde-par:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etikett** och **innehållstyp** tomma för tillfället.

7. Välj **Använd**.

## <a name="create-a-functions-app"></a>Skapa en funktionsapp

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. Sök efter och lägg till `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-paketet i projektet på fliken **Bläddra.** Om du inte hittar den markerar du kryssrutan **Inkludera förhandsversion.**

2. Öppna *Function1.cs*och lägg till namnområdena för .NET Core-konfigurationen och konfigurationsprovidern för appkonfiguration.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Lägg `static` till `Configuration` en egenskap med namnet `IConfiguration`för att skapa en singleton-instans av . Lägg sedan `static` till en konstruktor `AddAzureAppConfiguration()`för att ansluta till appkonfiguration genom att anropa . Detta kommer att läsa in konfigurationen en gång vid programstarten. Samma konfigurationsinstans används för alla functions-anrop senare.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Uppdatera `Run` metoden för att läsa värden från konfigurationen.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

1. Ange en miljövariabel med namnet **ConnectionString**och ange den till åtkomstnyckeln till appkonfigurationsarkivet. Om du använder kommandotolken i Windows kör du följande kommando och startar om kommandotolken så att ändringen kan börja gälla:

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

2. Tryck på F5 för att testa din funktion. Om du uppmanas till det godkänner du begäran från Visual Studio om att hämta och installera **CLI-verktyg (Azure Functions Core).** Du kan också behöva aktivera ett brandväggsundantag så att verktygen kan hantera HTTP-begäranden.

3. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret i webbläsaren på den lokala GET-begäran som returneras av funktionen.

    ![Snabbstart för lokal funktionsstart](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nytt App Configuration Store och använde det med en Azure Functions-app via [appkonfigurationsprovidern](https://go.microsoft.com/fwlink/?linkid=2074664). Om du vill veta hur du konfigurerar din Azure Functions-app för att dynamiskt uppdatera konfigurationsinställningar fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-azure-functions-csharp.md)
