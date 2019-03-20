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
ms.openlocfilehash: 22ec05660682f000d8bc3b9780732d5adf9b5c24
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226716"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Snabbstart: Skapa en Azure-funktion med App Configuration

Azure App Configuration är en hanterad konfigurationstjänst i Azure. Du kan använda den för att enkelt lagra och hantera alla programinställningarna på ett ställe som är avgränsade från din kod. Den här snabbstarten visar hur du införliva tjänsten i en Azure-funktion. 

Du kan använda valfri Kodredigerare för att utföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ tillgängligt på Windows, macOS och Linux-plattformar.

![Snabbstart för fullständig lokal](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här snabbstarten, installera [Visual Studio 2017](https://visualstudio.microsoft.com/vs). Kontrollera att även arbetsbelastningen **Azure-utveckling** är installerad. Även installera den [senaste Azure Functions-verktygen](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Skapa ett appkonfigurationsarkiv

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Skapa en funktionsapp

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Ansluta till en appbutik för konfiguration

1. Öppna *Function1.cs*, och Lägg till en referens till en App Configuration .NET Core-konfigurationsprovider.

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

1. Ange en miljövariabel som heter **ConnectionString**, och ange att snabbtangent i din appbutik för konfiguration. Om du använder Windows-Kommandotolken kör du följande kommando och starta om Kommandotolken så att ändringen ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux, kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Tryck på F5 för att testa funktionen. Om du får en uppmaning accepterar du begäran från Visual Studio för att ladda ned och installera **Azure Functions Core (CLI)**-verktyg. Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-begäranden.

3. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret i webbläsaren för att den lokala GET-begäran som returnerades av funktionen.

    ![Snabbstart för lokal funktionsstart](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapade en ny konfiguration appbutik och använder den med en Azure-funktion. Om du vill veta mer om hur du använder Appkonfiguration kan du fortsätta till nästa självstudie som visar autentisering.

> [!div class="nextstepaction"]
> [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
