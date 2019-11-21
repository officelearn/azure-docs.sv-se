---
title: Skapa din första beständiga funktion i Azure med hjälp av JavaScript
description: Skapa och publicera en beständig Azure-funktion med hjälp av Visual Studio Code.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: 94ba2830824c4a918e9451a9fc5140d422110370
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231315"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Skapa din första beständiga funktion i JavaScript

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

I den här artikeln får du lära dig hur du använder Azure Functions-tillägget för Visual Studio Code för att skapa och testa en beständig ”Hello World”-funktion lokalt.  Den här funktionen orkestrerar och kedjar samman anrop till andra funktioner. Du publicerar sedan funktionskoden till Azure.

![Köra beständiga funktioner i Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/download).

* Make sure you have the latest version of the [Azure Functions Core Tools](../functions-run-local.md).

* På en Windows-dator kontrollerar du att [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) är installerad och körs. På en Mac- eller Linux-dator måste du använda ett faktiskt Azure Storage-konto.

* Se till att du har version 8.0 eller en senare version av [Node.js](https://nodejs.org/) installerad.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Installera npm-paketet för Durable Functions

1. Installera npm-paketet för `durable-functions` genom att köra `npm install durable-functions` i funktionsappens rotkatalog.

## <a name="creating-your-functions"></a>Creating your functions

We'll now create the three functions you need to get started with Durable Functions: an HTTP starter, an orchestrator, and an activity function. The HTTP starter will initiate your entire solution, and the orchestrator will dispatch work to various activity functions.

### <a name="http-starter"></a>HTTP starter

Skapa först en HTTP-utlöst funktion som startar en orkestrering av beständig funktion.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Skapa en funktion](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions HTTP Starter** function template.

    ![Choose the HTTP starter template](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Leave the default name as `DurableFunctionsHttpStart` and press ****Enter**, then select **Anonymous** authentication.

    ![Välj anonym autentisering](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Nu har vi skapat en startpunkt till vår beständiga funktion. Vi lägger till en orkestrerare.

### <a name="orchestrator"></a>Orchestrator

Now, we'll create an orchestrator to coordinate activity functions.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Skapa en funktion](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions orchestrator** function template. Leave the name as the default "DurableFunctionsOrchestrator"

    ![Choose the orchestrator template](./media/quickstart-js-vscode/create-function-choose-template.png)

Vi har lagt till en orkestrerare för att samordna aktivitetsfunktioner. Nu lägger vi till den refererade aktivitetsfunktionen.

### <a name="activity"></a>Aktivitet

Now, we'll create an activity function to actually carry out the work of the solution.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Skapa en funktion](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions activity** function template. Leave the name as the default "Hello".

    ![Choose the activity template](./media/quickstart-js-vscode/create-function-choose-template.png)

Nu har vi lagt till alla komponenter som behövs för att påbörja en orkestrering och kedja ihop aktivitetsfunktioner.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio Code.

1. On a Windows computer, start the Azure Storage Emulator and make sure that the **AzureWebJobsStorage** property of *local.settings.json* is set to `UseDevelopmentStorage=true`.

    For Storage Emulator 5.8 make sure that the **AzureWebJobsSecretStorageType** property of local.settings.json is set to `files`. On     a Mac or Linux computer, you must set the **AzureWebJobsStorage** property to the connection string of an existing Azure storage         account. Du skapar ett lagringskonto senare i den här artikeln.

2. Ange en brytpunkt i funktionskoden och tryck på F5 för att starta funktionsappsprojektet om du vill testa funktionen. Utdata från Core Tools visas på panelen **Terminal**. Om det här är första gången du Durable Functions installeras Durable Functions-tillägget, och bygget kan ta några sekunder.

    > [!NOTE]
    > JavaScript Durable Functions kräver version **1.7.0** eller senare av tillägget **Microsoft.Azure.WebJobs.Extensions.DurableTask**. Run the following command from the root folder of your Azure Functions app to install the Durable Functions extension `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Ersätt `{functionName}` med `DurableFunctionsOrchestrator`.

5. Using a tool like [Postman](https://www.getpostman.com/) or [cURL](https://curl.haxx.se/), send an HTTP POST request to the URL endpoint.

   Svaret är det första resultatet från HTTP-funktionen, som anger att den beständiga orkestreringen har startats korrekt. Det är inte ännu slutresultatet av orkestreringen. Svaret innehåller några användbara URL:er. För tillfället kör vi en fråga om orkestreringens status.

6. Copy the URL value for `statusQueryGetUri` and paste it in the browser's address bar and execute the request. Alternatively you can also continue to use Postman to issue the GET request.

   Begäran kör en fråga mot orkestreringsinstansen om statusen. You should get an eventual response, which shows us the instance has completed, and includes the outputs or results of the durable function. It looks like: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. To stop debugging, press **Shift + F5** in VS Code.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Du bör få samma statussvar som förut när du använder den publicerade appen.

## <a name="next-steps"></a>Nästa steg

Du har använt Visual Studio Code för att skapa och publicera en beständig JavaScript-funktionsapp.

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)