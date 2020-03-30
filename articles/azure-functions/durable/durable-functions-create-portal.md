---
title: Skapa varaktiga funktioner med Azure-portalen
description: Lär dig hur du installerar tillägget Varaktiga funktioner för Azure Functions för portalutveckling.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769650"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Skapa varaktiga funktioner med Azure-portalen

Tillägget [Varaktiga funktioner](durable-functions-overview.md) för Azure-funktioner finns i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Det här tillägget måste vara installerat i funktionsappen. Den här artikeln visar hur du installerar det här paketet så att du kan utveckla varaktiga funktioner i Azure-portalen.

> [!NOTE]
> 
> * Om du utvecklar hållbara funktioner i C#, bör du istället överväga [Visual Studio 2019 utveckling](durable-functions-create-first-csharp.md).
> * Om du utvecklar hållbara funktioner i JavaScript bör du i stället överväga utveckling av [Visual Studio-kod](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp för att vara värd för körningen av alla funktioner. Med en funktionsapp kan du gruppera dina funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser. Du kan skapa en .NET- eller JavaScript-app.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Som standard använder funktionsappen som skapats version 2.x av Azure Functions runtime. Tillägget Varaktiga funktioner fungerar på båda versionerna 1.x och 2.x av Azure Functions-körningen i C#och version 2.x i JavaScript. Mallar är dock bara tillgängliga när du inriktar dig på version 2.x av körningen oavsett vilket språk som valts.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Installera npm-paketet med varaktiga funktioner (endast JavaScript)

Om du skapar JavaScript Durable Functions måste du installera [ `durable-functions` npm-paketet](https://www.npmjs.com/package/durable-functions).

1. Välj namnet på funktionsappen, följt av **plattformsfunktioner**och sedan **avancerade verktyg (Kudu).**

   ![Funktioner plattform funktioner välja Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Inuti Kudu-konsolen väljer du **Felsökningskonsol** och sedan **CMD**.

   ![Kudu felsökningskonsol](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Funktionsappens filkatalogstruktur ska visas. Gå till mappen `site/wwwroot`. Därifrån kan du `package.json` ladda upp en fil genom att dra och släppa den i filkatalogfönstret. Ett `package.json` prov finns nedan:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu ladda upp package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. När `package.json` du har laddat `npm install` upp kör du kommandot från Kudu Remote Execution Console.

   ![Kudu kör npm installera](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Skapa en orchestrator-funktion

1. Expandera funktionsappen **+** och klicka på knappen bredvid **Funktioner**. Om det här är den första funktionen i din funktionsapp väljer du **I portalen** och sedan **Fortsätt**. Annars går du till steg tre.

   ![Sidan snabbstart för funktioner i Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Välj **Fler mallar** och sedan **Slutför och visa mallar**.

    ![Functions-snabbstart, välj fler mallar](./media/durable-functions-create-portal/add-first-function.png)

1. Skriv och välj `durable` httpstartmallen för varaktiga funktioner i sökfältet och välj sedan **http-startmallen För varaktiga funktioner.**

1. När du uppmanas till det väljer du **Installera** för att installera Azure DurableTask-tillägget och eventuella beroenden i funktionsappen. Du behöver bara installera tillägget en gång för en given funktionsapp. När installationen är klar väljer du **Fortsätt**.

    ![Installera bindningstillägg](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. När installationen är klar namnger `HttpStart` du den nya funktionen och väljer **Skapa**. Funktionen som skapas används för att starta orkestreringen.

1. Skapa en annan funktion i funktionsappen, den här gången med hjälp av mallen **Varaktiga funktioner Orchestrator.** Ge den nya `HelloSequence`orkestreringsfunktionen ett namn .

1. Skapa en tredje `Hello` funktion med namnet med hjälp av mallen **Aktivitet för varaktiga funktioner.**

## <a name="test-the-durable-function-orchestration"></a>Testa den hållbara funktionen orkestrering

1. Gå tillbaka till **httpstart-funktionen,** välj **</> Hämta funktions-URL:en** och **Kopiera** webbadressen. Du använder den här URL:en för att starta **HelloSequence-funktionen.**

1. Använd ett HTTP-verktyg som Postman eller cURL för att skicka en POST-begäran till webbadressen som du kopierade. Följande exempel är ett cURL-kommando som skickar en POST-begäran till funktionen varaktiga funktioner:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    I det `{your-function-app-name}` här exemplet är den domän som är namnet på din funktionsapp. Svarsmeddelandet innehåller en uppsättning URI-slutpunkter som du kan använda för att övervaka och hantera körningen, som ser ut som följande exempel:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Anropa `statusQueryGetUri` slutpunkts-URI:n och du ser den aktuella statusen för den varaktiga funktionen, som kan se ut så här:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Fortsätt att `statusQueryGetUri` anropa slutpunkten tills statusen ändras till **Slutförd**och du ser ett svar som följande exempel:

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

Din första hållbara funktion är nu igång i Azure.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)
