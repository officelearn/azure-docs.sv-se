---
title: Skapa din första beständiga funktion i Azure med hjälp av JavaScript
description: Skapa och publicera en beständig Azure-funktion med hjälp av Visual Studio Code.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: glenga
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: c54a5631222a6de261e9805f284a4dfa2801750f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612913"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Skapa din första beständiga funktion i JavaScript

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter.

I den här artikeln får du lära dig hur du använder Azure Functions-tillägget för Visual Studio Code för att skapa och testa en beständig ”Hello World”-funktion lokalt.  Den här funktionen orkestrerar och kedjar samman anrop till andra funktioner. Du publicerar sedan funktionskoden till Azure.

![Köra beständiga funktioner i Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/download).

* Kontrollera att du har den senaste versionen av den [Azure Functions Core Tools](../functions-run-local.md).

* På en Windows-dator kontrollerar du att [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) är installerad och körs. På en Mac- eller Linux-dator måste du använda ett faktiskt Azure Storage-konto.

* Se till att du har version 8.0 eller en senare version av [Node.js](https://nodejs.org/) installerad.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Installera npm-paketet för Durable Functions

1. Installera npm-paketet för `durable-functions` genom att köra `npm install durable-functions` i funktionsappens rotkatalog.

## <a name="creating-your-functions"></a>Skapa dina funktioner

Nu ska vi skapa de tre funktionerna som du behöver för att komma igång med varaktiga funktioner: en HTTP-starter, en initierare och funktion som en aktivitet. HTTP-starter initierar hela lösningen och orchestrator kommer skicka ut arbete till olika Aktivitetsfunktioner.

### <a name="http-starter"></a>HTTP-starter

Skapa först en HTTP-utlöst funktion som startar en orkestrering av beständig funktion.

1. I *Azure: Functions*, Välj den **Create Function** ikon.

    ![Skapa en funktion](./media/quickstart-js-vscode/create-function.png)

2. Välj mappen med ditt funktionsappsprojekt och välj den **HTTP-Starter för beständiga funktioner** funktionsmallen.

    ![Välj den HTTP-starter-mallen](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Lämna standardnamnet som `DurableFunctionsHttpStart` och tryck på ** **RETUR** och välj sedan **anonym** autentisering.

    ![Välj anonym autentisering](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Nu har vi skapat en startpunkt till vår beständiga funktion. Vi lägger till en orkestrerare.

### <a name="orchestrator"></a>Orchestrator

Nu ska skapa vi en orchestrator för att samordna Aktivitetsfunktioner.

1. I *Azure: Functions*, Välj den **Create Function** ikon.

    ![Skapa en funktion](./media/quickstart-js-vscode/create-function.png)

2. Välj mappen med ditt funktionsappsprojekt och välj den **varaktiga funktioner orchestrator** funktionsmallen. Lämna namnet som standard ”DurableFunctionsOrchestrator”

    ![Välj den orchestrator-mallen](./media/quickstart-js-vscode/create-function-choose-template.png)

Vi har lagt till en orkestrerare för att samordna aktivitetsfunktioner. Nu lägger vi till den refererade aktivitetsfunktionen.

### <a name="activity"></a>Aktivitet

Nu ska skapa vi en aktivitet funktion att faktiskt utföra verk som tillhör lösningen.

1. I *Azure: Functions*, Välj den **Create Function** ikon.

    ![Skapa en funktion](./media/quickstart-js-vscode/create-function.png)

2. Välj mappen med ditt funktionsappsprojekt och välj den **varaktiga funktioner aktivitet** funktionsmallen. Lämna namnet som standard ”Hello”.

    ![Välj mall för aktivitet](./media/quickstart-js-vscode/create-function-choose-template.png)

Nu har vi lagt till alla komponenter som behövs för att påbörja en orkestrering och kedja ihop aktivitetsfunktioner.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio Code.

1. Starta Azure Storage-emulatorn på en Windows-dator och se till att den **AzureWebJobsStorage** egenskapen för *local.settings.json* är inställd på `UseDevelopmentStorage=true`.

    För Storage-emulatorn 5.8 se till att den **AzureWebJobsSecretStorageType** egenskapen för local.settings.json har angetts `files`. På en Mac- eller Linux-dator måste du ange den **AzureWebJobsStorage** egenskapen för anslutningssträngen för ett befintligt Azure storage-konto. Du skapar ett lagringskonto senare i den här artikeln.

2. Ange en brytpunkt i funktionskoden och tryck på F5 för att starta funktionsappsprojektet om du vill testa funktionen. Utdata från Core Tools visas på panelen **Terminal**. Om det här är första gången du Durable Functions installeras Durable Functions-tillägget, och bygget kan ta några sekunder.

    > [!NOTE]
    > JavaScript Durable Functions kräver version **1.7.0** eller senare av tillägget **Microsoft.Azure.WebJobs.Extensions.DurableTask**. Kör följande kommando från rotmappen för din Azure Functions-app för att installera tillägget varaktiga funktioner `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Ersätt `{functionName}` med `DurableFunctionsOrchestrator`.

5. Med ett verktyg som [Postman](https://www.getpostman.com/) eller [cURL](https://curl.haxx.se/), skicka en HTTP POST-begäran till URL-slutpunkten.

   Svaret är det första resultatet från HTTP-funktionen, som anger att den beständiga orkestreringen har startats korrekt. Det är inte ännu slutresultatet av orkestreringen. Svaret innehåller några användbara URL:er. För tillfället kör vi en fråga om orkestreringens status.

6. Kopiera URL-värdet för `statusQueryGetUri` och klistra in den i webbläsarens adressfält och kör din begäran. Alternativt kan du också fortsätta att använda Postman för att utfärda en GET-begäran.

   Begäran kör en fråga mot orkestreringsinstansen om statusen. Du bör få ett eventuell svar som visar oss instansen har slutförts och innehåller utdata eller resultatet av funktionen varaktiga. Det ser ut som: 

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

7. Om du vill stoppa felsökningen trycker du på **SKIFT + F5** i VS Code.

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
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-concepts.md)
