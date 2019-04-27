---
title: Skapa varaktiga funktioner med hjälp av Azure portal
description: Lär dig hur du installerar tillägget varaktiga funktioner för Azure Functions för utveckling av portalen.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 10/23/2018
ms.date: 03/25/2019
ms.author: v-junlch
ms.openlocfilehash: 1c60bd4dae6c279ccff637ff0aa798c48ebec6f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710956"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Skapa varaktiga funktioner med hjälp av Azure portal

Den [varaktiga funktioner](durable-functions-overview.md) tillägget för Azure Functions finns i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Det här tillägget måste installeras i din funktionsapp. Den här artikeln visar hur du installerar det här paketet så att du kan utveckla varaktiga funktioner i Azure-portalen.

> [!NOTE]
> 
> * Om du utvecklar varaktiga funktioner i C#, i stället bör du [Visual Studio 2017 utveckling](durable-functions-create-first-csharp.md).
> * Om du utvecklar varaktiga funktioner i JavaScript, i stället bör du [Visual Studio Code utveckling](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av en funktion. En funktionsapp kan du gruppera dina funktioner som en logisk enhet för enklare hantering, distribution och dela resurser. Du kan skapa en .NET eller JavaScript-app.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Som standard använder funktionsappen skapas version 2.x av Azure Functions-körningen. Tillägget varaktiga funktioner fungerar på båda versionerna 1.x och 2.x av Azure Functions-körning i C#, och version 2.x i JavaScript. Mallar är dock endast tillgängliga när med version 2.x av körningstillfället oavsett det valda språket.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Installera durable-functions npm-paket (JavaScript)

Om du skapar varaktiga funktioner i JavaScript, behöver du installera den [ `durable-functions` npm-paketet](https://www.npmjs.com/package/durable-functions).

1. Välj funktionsappens namn följt av **plattformsfunktioner**, sedan **avancerade verktyg (Kudu)**.

   ![Functions plattformsfunktioner väljer Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Inuti Kudu-konsolen väljer **Felsökningskonsolen** sedan **CMD**.

   ![Kudu-Felsökningskonsolen](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Din funktionsapp filstruktur bör visas. Gå till mappen `site/wwwroot`. Därifrån kan du ladda upp en `package.json` fil genom att dra och släppa den i fönstret fil directory. Ett sampel `package.json` är lägre än:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.1.2"
      }
    }
    ```

   ![Kudu överför package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. När din `package.json` har överförts, kör den `npm install` från Kudu-konsolen för fjärråtkomst körning.

   ![Kudu kör npm install](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Skapa en orchestrator-funktion

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det här är den första funktionen i din funktionsapp väljer du **I portalen** och sedan **Fortsätt**. Annars går du till steg tre.

   ![Sidan snabbstart för funktioner i Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Välj **Fler mallar** och sedan **Slutför och visa mallar**.

    ![Functions-snabbstart, välj fler mallar](./media/durable-functions-create-portal/add-first-function.png)

1. I sökfältet skriver `durable` och välj sedan den **varaktiga funktioner HTTP-starter** mall.

1. När du uppmanas, väljer **installera** att installera tillägget Azure DurableTask eventuella beroenden i funktionsappen. Du behöver bara installera tillägget en gång för en ge funktionsapp. När installationen är klar väljer du **Fortsätt**.

    ![Installera bindningstillägg](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. När installationen är klar, namnge den nya funktionen `HttpStart` och välj **skapa**. Funktionen skapas används för att starta dirigering.

1. Skapa en annan funktion i funktionsappen, den här gången med hjälp av den **Orchestrator för beständiga funktioner** mall. Namnge den nya orchestration-funktionen `HelloSequence`.

1. Skapa en tredje funktion med namnet `Hello` med hjälp av den **aktivitet för beständiga funktioner** mall.

## <a name="test-the-durable-function-orchestration"></a>Testa varaktiga funktionen-dirigering

1. Gå tillbaka till den **HttpStart** fungera, Välj **<> / hämta Funktionswebbadress** och **kopia** URL: en. Använd den här URL: en för att starta den **HelloSequence** funktion.

1. Använda en HTTP-verktyg som Postman eller cURL för att skicka en POST-begäran till den URL som du kopierade. I följande exempel är ett cURL-kommando som skickar en POST-begäran till funktionen varaktiga:

    ```bash
    curl -X POST https://{your-function-app-name}.chinacloudsites.cn/api/orchestrators/HelloSequence
    ```

    I det här exemplet `{your-function-app-name}` är den domän som är namnet på din funktionsapp. Svarsmeddelandet innehåller en uppsättning URI-slutpunkter som du kan använda för att övervaka och hantera körningen som ser ut som i följande exempel:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Anropa den `statusQueryGetUri` URI-slutpunkten och du se aktuell status varaktiga funktionen, som kan se ut så här:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Fortsätta anropar den `statusQueryGetUri` slutpunkten tills status ändras till **slutförd**, och du ser ett svar som i följande exempel:

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

Din första varaktiga funktion är nu aktiv och körs i Azure.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-concepts.md)

<!-- Update_Description: wording update -->
