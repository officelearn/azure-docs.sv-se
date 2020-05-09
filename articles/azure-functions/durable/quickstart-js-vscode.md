---
title: Skapa din första beständiga funktion i Azure med hjälp av JavaScript
description: Skapa och publicera en beständig Azure-funktion med hjälp av Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 05/07/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 6544cd115dbae2268492a8775a780d2f045f4e4a
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889682"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Skapa din första beständiga funktion i JavaScript

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter.

I den här artikeln får du lära dig hur du använder Azure Functions-tillägget för Visual Studio Code för att skapa och testa en beständig ”Hello World”-funktion lokalt.  Den här funktionen orkestrerar och kedjar samman anrop till andra funktioner. Du publicerar sedan funktionskoden till Azure.

![Köra beständiga funktioner i Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/download).

* Installera [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code-tillägget

* Kontrol lera att du har den senaste versionen av [Azure Functions Core tools](../functions-run-local.md).

* Durable Functions kräver ett Azure Storage-konto. Du behöver en Azure-prenumeration.

* Kontrol lera att du har version 10. x eller 12. x av [Node. js](https://nodejs.org/) installerat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt 

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt. 

1. Tryck på F1 (eller Ctrl + Cmd + Shift + P) i Visual Studio Code för att öppna kommando paletten. I paletten kommando söker du efter och väljer `Azure Functions: Create New Project...`.

    ![Skapa funktion](media/quickstart-js-vscode/functions-create-project.png)

1. Välj en tom mapplats för ditt projekt och välj **Välj**.

1. Ange följande information i instruktionerna nedan:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj ett språk för ditt projekt för Function-appen | JavaScript | Skapa ett lokalt Node. js-Function-projekt. |
    | Välj en version | Azure Functions v3 | Du ser bara det här alternativet när kärn verktygen inte redan har installerats. I det här fallet installeras kärn verktyg första gången du kör appen. |
    | Välj en mall för projektets första funktion | Hoppa över nu | |
    | Välj hur du vill öppna projektet | Öppna i aktuellt fönster | Öppnar VS-kod i den mapp som du har valt. |

Visual Studio Code installerar Azure Functions Core Tools, om det behövs. Det skapar också ett app-projekt i en mapp. Det här projektet innehåller konfigurationsfilerna [Host. JSON](../functions-host-json.md) och [Local. Settings. JSON](../functions-run-local.md#local-settings-file) .

En Package. JSON-fil skapas också i rotmappen.

### <a name="enable-compatibility-mode"></a>Aktivera kompatibilitetsläge

Java Script Durable Functions kräver för närvarande att ett Azure Functions v2-kompatibilitetsläge är aktiverat.

1. Öppna *Local. Settings. JSON* för att redigera inställningarna som används när du kör appen lokalt.

1. Lägg till en inställning `FUNCTIONS_V2_COMPATIBILITY_MODE` med namnet med värdet `true`.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureWebJobsStorage": "",
            "FUNCTIONS_WORKER_RUNTIME": "node",
            "FUNCTIONS_V2_COMPATIBILITY_MODE": "true"
        }
    }
    ```

## <a name="install-the-durable-functions-npm-package"></a>Installera npm-paketet för Durable Functions

Om du vill arbeta med Durable Functions i en Node. js-Function-app använder du `durable-functions`ett bibliotek som kallas.

1. Använd menyn *Visa* eller Ctrl + Skift + ' för att öppna en ny terminal i vs Code.

1. Installera npm-paketet för `durable-functions` genom att köra `npm install durable-functions` i funktionsappens rotkatalog.

## <a name="creating-your-functions"></a>Skapa dina funktioner

Den mest grundläggande Durable Functions-appen innehåller tre funktioner:

* *Orchestrator-funktion* – beskriver ett arbets flöde som dirigerar andra funktioner.
* *Aktivitets funktion* – anropas av Orchestrator-funktionen, utför arbete och kan eventuellt returnera ett värde.
* *Klient funktion* – en vanlig Azure-funktion som startar en Orchestrator-funktion. I det här exemplet används en HTTP-utlöst funktion.

### <a name="orchestrator-function"></a>Orchestrator-funktion

Du använder en mall för att skapa en hållbar funktions kod i projektet.

1. I paletten kommando söker du efter och väljer `Azure Functions: Create Function...`.

1. Ange följande information i instruktionerna nedan:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Durable Functions Orchestrator | Skapa ett Durable Functions Orchestration |
    | Ange ett funktions namn | HelloOrchestrator | Namnet på den varaktiga funktionen |

Du har lagt till en Orchestrator för att koordinera aktivitets funktioner. Öppna *HelloOrchestrator/index. js* för att se Orchestrator-funktionen. Varje anrop för `context.df.callActivity` att anropa en aktivitets funktion med `Hello`namnet.

Sedan lägger du till funktionen refererad `Hello` aktivitet.

### <a name="activity-function"></a>Aktivitets funktion

1. I paletten kommando söker du efter och väljer `Azure Functions: Create Function...`.

1. Ange följande information i instruktionerna nedan:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Durable Functions aktivitet | Skapa en aktivitets funktion |
    | Ange ett funktions namn | Hello | Namn på din aktivitets funktion |

Du har lagt till `Hello` aktivitets funktionen som anropas av Orchestrator. Öppna *Hello/index. js* för att se att det tar ett namn som inmatat och returnerar en hälsning. En aktivitets funktion är den plats där du utför åtgärder som att göra ett databas anrop eller utföra en beräkning.

Slutligen lägger du till en HTTP-utlöst funktion som startar dirigeringen.

### <a name="client-function-http-starter"></a>Klient funktion (HTTP starter)

1. I paletten kommando söker du efter och väljer `Azure Functions: Create Function...`.

1. Ange följande information i instruktionerna nedan:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Durable Functions HTTP-startstarter | Skapa en HTTP-startfunktion |
    | Ange ett funktions namn | DurableFunctionsHttpStart | Namn på din aktivitets funktion |
    | Auktorisationsnivå | Anonym | I demonstrations syfte tillåter du att funktionen anropas utan autentisering |

Du har lagt till en HTTP-utlöst funktion som startar ett dirigering. Öppna *DurableFunctionsHttpStart/index. js* för att se att den `client.startNew` använder för att starta en ny dirigering. Sedan använder `client.createCheckStatusResponse` den för att returnera ett HTTP-svar som innehåller URL: er som kan användas för att övervaka och hantera den nya dirigeringen.

Nu har du en Durable Functions-app som kan köras lokalt och distribueras till Azure.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio Code.

1. Om du vill testa funktionen ställer du in en Bryt `Hello` punkt i aktivitetens funktions kod (*Hello/index. js*). Tryck på F5 eller `Debug: Start Debugging` Välj från kommando paletten för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**.

    > [!NOTE]
    > Mer information om fel sökning hittar du i [Durable Functions Diagnostics](durable-functions-diagnostics.md#debugging) .

1. Durable Functions kräver att ett Azure Storage-konto körs. Vid VS-kod kan du välja ett lagrings konto genom att välja **Välj lagrings konto**.

    ![Skapa lagringskonto](media/quickstart-js-vscode/functions-select-storage.png)

1. Följ instruktionerna och ange följande information för att skapa ett nytt lagrings konto i Azure.

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en prenumeration | *namnet på din prenumeration* | Välj din Azure-prenumeration |
    | Välj ett lagrings konto | Skapa ett nytt lagringskonto |  |
    | Ange namnet på det nya lagrings kontot | *unikt namn* | Namn på det lagrings konto som ska skapas |
    | Välj en resursgrupp | *unikt namn* | Namnet på den resurs grupp som ska skapas |
    | Välja en plats | *regionen* | Välj en region nära dig |

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](media/quickstart-js-vscode/functions-f5.png)

1. Skicka en HTTP POST-begäran till URL-slutpunkten med hjälp av ett verktyg som [Postman](https://www.getpostman.com/) eller [sväng](https://curl.haxx.se/). Ersätt det sista segmentet med namnet på Orchestrator-funktionen (`HelloOrchestrator`). URL-adressen bör liknas `http://localhost:7071/api/orchestrators/HelloOrchestrator`.

   Svaret är det första resultatet från HTTP-funktionen som gör att du vet att den beständiga dirigeringen har startat. Det är inte ännu slutresultatet av orkestreringen. Svaret innehåller några användbara URL:er. För tillfället kör vi en fråga om orkestreringens status.

1. Kopiera URL-värdet för `statusQueryGetUri` och klistra in det i webbläsarens Adress fält och kör begäran. Alternativt kan du även fortsätta att använda Postman för att utfärda GET-begäran.

   Begäran kör en fråga mot orkestreringsinstansen om statusen. Du får ett möjligt svar som visar att instansen har slutförts och innehåller utdata eller resultat från den varaktiga funktionen. Det ser ut så här: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Stoppa fel sökningen genom att trycka på **SKIFT + F5** i vs Code.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

### <a name="enable-compatibility-mode"></a>Aktivera kompatibilitetsläge

Samma Azure Functions v2-kompatibilitet som du har aktiverat lokalt måste vara aktive rad i appen i Azure.

1. Med hjälp av kommando-paletten söker du `Azure Functions: Edit Setting...`efter och väljer.

1. Följ anvisningarna för att hitta din Function-app i din Azure-prenumeration.

1. Välj `Create new App Setting...`.

1. Ange en ny inställnings nyckel `FUNCTIONS_V2_COMPATIBILITY_MODE`i.

1. Ange ett inställnings värde `true`för.

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:`http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Du bör få samma statussvar som förut när du använder den publicerade appen.

## <a name="next-steps"></a>Nästa steg

Du har använt Visual Studio Code för att skapa och publicera en beständig JavaScript-funktionsapp.

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)