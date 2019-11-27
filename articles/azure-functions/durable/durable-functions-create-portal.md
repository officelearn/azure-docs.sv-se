---
title: Skapa Durable Functions med hjälp av Azure Portal
description: Lär dig hur du installerar Durable Functions-tillägget för Azure Functions för Portal utveckling.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: eaa241eff6e1c359045a0ea3d8871fde6c60a059
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231487"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Skapa Durable Functions med hjälp av Azure Portal

[Durable Functions](durable-functions-overview.md) -tillägget för Azure Functions anges i NuGet-paketet [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Tillägget måste vara installerat i din Function-app. Den här artikeln visar hur du installerar det här paketet så att du kan utveckla varaktiga funktioner i Azure Portal.

> [!NOTE]
> 
> * Om du utvecklar varaktiga funktioner i C#bör du i stället överväga att [utveckla Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Om du utvecklar varaktiga funktioner i Java Script bör du i stället överväga [Visual Studio Code Development](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en Function-app som är värd för körningen av en funktion. Med en Function-app kan du gruppera dina funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser. Du kan skapa en .NET-eller JavaScript-app.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Som standard använder Function-appen version 2. x av Azure Functions Runtime. Durable Functions-tillägget fungerar både i version 1. x och 2. x i Azure Functions runtime i C#, och version 2. x i Java Script. Men mallarna är bara tillgängliga när du riktar in dig på version 2. x av körnings miljön, oavsett vilket språk som valts.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Installera NPM-paketet bestående av funktioner (endast java script)

Om du skapar ett JavaScript-Durable Functions måste du installera [`durable-functions` NPM-paketet](https://www.npmjs.com/package/durable-functions).

1. Välj appens funktions namn, följt av **plattforms funktioner**, och sedan **Avancerade verktyg (kudu)** .

   ![Funktions plattforms funktioner Välj kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. I kudu-konsolen väljer du **Felsök konsol** och sedan **cmd**.

   ![Kudu fel söknings konsol](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Din funktions programmets fil katalog struktur bör visas. Gå till mappen `site/wwwroot`. Därifrån kan du ladda upp en `package.json`-fil genom att dra och släppa den i fönstret fil katalog. Ett exempel `package.json` är nedan:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu upload Package. JSON](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. När `package.json` har överförts kör du kommandot `npm install` från konsolen för fjärrkörning av kudu.

   ![Kudu kör NPM-installation](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Skapa en Orchestrator-funktion

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det här är den första funktionen i din funktionsapp väljer du **I portalen** och sedan **Fortsätt**. Annars går du till steg tre.

   ![Sidan snabbstart för funktioner i Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Välj **Fler mallar** och sedan **Slutför och visa mallar**.

    ![Functions-snabbstart, välj fler mallar](./media/durable-functions-create-portal/add-first-function.png)

1. Skriv `durable` i Sök fältet och välj sedan **Durable Functions http** startmall.

1. När du uppmanas väljer du **Installera** för att installera Azure DurableTask-tillägget alla beroenden i Function-appen. Du behöver bara installera tillägget en gång för en app för att ge en funktion. När installationen är klar väljer du **Fortsätt**.

    ![Installera bindningstillägg](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. När installationen är klar namnger du den nya funktionen `HttpStart` och väljer **skapa**. Funktionen som skapas används för att starta dirigeringen.

1. Skapa en annan funktion i Function-appen, den här gången med hjälp av **Durable Functions Orchestrator** -mallen. Ge den nya Orchestration-funktionen `HelloSequence`.

1. Skapa en tredje funktion med namnet `Hello` med hjälp av mallen **Durable Functions aktivitet** .

## <a name="test-the-durable-function-orchestration"></a>Testa den varaktiga funktions dirigeringen

1. Gå tillbaka till funktionen **HttpStart** , Välj **</> Hämta funktions webb adress** och **Kopiera** URL: en. Du kan använda den här URL: en för att starta funktionen **HelloSequence** .

1. Använd ett HTTP-verktyg som Postman eller sväng för att skicka en POST-begäran till den URL som du kopierade. Följande exempel är ett spiral kommando som skickar en POST-begäran till den varaktiga funktionen:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    I det här exemplet är `{your-function-app-name}` den domän som är namnet på din Function-app. Svarsmeddelandet innehåller en uppsättning URI-slutpunkter som du kan använda för att övervaka och hantera körningen, vilket ser ut som i följande exempel:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Anropa `statusQueryGetUri` slut punkts-URI: n och se aktuell status för den varaktiga funktionen, som kan se ut så här:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Fortsätt att anropa `statusQueryGetUri` slut punkten tills statusen ändras till **slutförd**, så ser du ett svar som i följande exempel:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Din första tåliga funktion är nu igång i Azure.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)
