---
title: Snabbstart för Azure App Configuration med Azure Functions | Microsoft Docs
description: En snabbstart för användning av Azure App Configuration med Azure Functions.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5f28e213a5f824562df62a05b98f0f92f71bc591
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957444"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Snabbstart: Skapa en Azure-funktion med App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Med den kan du enkelt lagra och hantera alla programinställningar på ett ställe som är separat från din kod. Den här snabbstarten visar hur du införlivar tjänsten i en Azure-funktion. 

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är dock ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

![Quickstart Complete local](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten installerar du [Visual Studio 2017](https://visualstudio.microsoft.com/vs) (och se till att arbetsbelastningen **Azure Development** (Azure-utveckling) också installeras) samt [de senaste Azure Functions-verktygen](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Skapa en funktionsapp

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-app-configuration-store"></a>Ansluta till ett appkonfigurationsarkiv

1. Öppna *Function1.cs* och lägg till en referens till App Configuration .NET Core-konfigurationsprovidern.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. Uppdatera metoden `Run` till att använda App Configuration genom att anropa `builder.AddAzureAppConfiguration()`.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

1. Ange en miljövariabel som heter **ConnectionString** till åtkomstnyckeln för ditt appkonfigurationsarkiv. Om du använder Windows-kommandotolken kör du följande kommando och startar om Kommandotolken så att ändringen börjar gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Testa funktionen genom att trycka på **F5**. Om du får en uppmaning accepterar du begäran från Visual Studio för att ladda ned och installera **Azure Functions Core (CLI)**-verktyg. Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-förfrågningar.

3. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Nedan visas svaret på den lokala GET-begäran som returnerades av funktionen i webbläsaren:

    ![Snabbstart för lokal funktionsstart](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett nytt appkonfigurationsarkiv och använt det med en Azure-funktion. Om du vill lära dig mer om att använda App Configuration fortsätter du till nästa självstudie som visar hur autentisering går till.

> [!div class="nextstepaction"]
> [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
