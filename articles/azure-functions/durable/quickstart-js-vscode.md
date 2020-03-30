---
title: Skapa din första beständiga funktion i Azure med hjälp av JavaScript
description: Skapa och publicera en beständig Azure-funktion med hjälp av Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 03/24/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 55098daa69d3e878140b20095b0a3e08811269e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257656"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Skapa din första beständiga funktion i JavaScript

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

I den här artikeln får du lära dig hur du använder Azure Functions-tillägget för Visual Studio Code för att skapa och testa en beständig ”Hello World”-funktion lokalt.  Den här funktionen orkestrerar och kedjar samman anrop till andra funktioner. Du publicerar sedan funktionskoden till Azure.

![Köra beständiga funktioner i Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/download).

* Installera [azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS-kodtillägget

* Kontrollera att du har den senaste versionen av [Azure Functions Core Tools](../functions-run-local.md).

* Varaktiga funktioner kräver ett Azure-lagringskonto. Du behöver en Azure-prenumeration.

* Kontrollera att du har installerat version 10.x eller 12.x [node.js.](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt 

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt. 

1. Öppna kommandopaletten genom att trycka på F1 (eller Ctrl/Cmd+Skift+P) i Visual Studio-kod. Sök efter och välj `Azure Functions: Create New Project...`i kommandopaletten .

    ![Skapa funktion](media/quickstart-js-vscode/functions-create-project.png)

1. Välj en tom mappplats för projektet och välj **Välj**.

1. Ange följande information efter anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj ett språk för funktionsappprojektet | JavaScript | Skapa ett lokalt nod.js-funktionsprojekt. |
    | Välj en version | Azure-funktioner v3 | Du ser bara det här alternativet när kärnverktygen inte redan är installerade. I det här fallet installeras Core Tools första gången du kör appen. |
    | Välj en mall för projektets första funktion | Hoppa över för tillfället | |
    | Välj hur du vill öppna projektet | Öppna i aktuellt fönster | Öppnar VS-kod igen i den valda mappen. |

Visual Studio Code installerar Azure Functions Core Tools om det behövs. Det skapar också ett funktionsappprojekt i en mapp. Det här projektet innehåller konfigurationsfilerna [host.json](../functions-host-json.md) och [local.settings.json.](../functions-run-local.md#local-settings-file)

En package.json-fil skapas också i rotmappen.

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Aktivera Kompatibilitetsläge för Azure Functions V2

För närvarande kräver JavaScript durable functions Azure Functions V2-kompatibilitetsläge aktiveras.

1. Öppna *local.settings.json* om du vill redigera de inställningar som används när du kör appen lokalt.

1. Lägg till `FUNCTIONS_V2_COMPATIBILITY_MODE` en inställning `true`med namnet .

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

Om du vill arbeta med varaktiga funktioner i en nod.js-funktionsapp använder du ett bibliotek som heter `durable-functions`.

1. Öppna en ny terminal i VS-kod med *Visa-menyn* eller Ctrl+Skift+' .

1. Installera npm-paketet för `durable-functions` genom att köra `npm install durable-functions` i funktionsappens rotkatalog.

## <a name="creating-your-functions"></a>Skapa dina funktioner

Den mest grundläggande appen Hållbara funktioner innehåller tre funktioner:

* *Orchestrator-funktionen* - beskriver ett arbetsflöde som dirigerar andra funktioner.
* *Aktivitetsfunktion* - anropad av orchestrator-funktionen, utför arbete och returnerar eventuellt ett värde.
* *Klientfunktion* - en vanlig Azure-funktion som startar en orchestrator-funktion. I det här exemplet används en HTTP-utlöst funktion.

### <a name="orchestrator-function"></a>Orchestrator (funktionen Orchestrator)

Du använder en mall för att skapa den varaktiga funktionskoden i projektet.

1. Sök efter och välj `Azure Functions: Create Function...`i kommandopaletten .

1. Ange följande information efter anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Innehavare funktioner orchestrator | Skapa en orkestrering med varaktiga funktioner |
    | Ange ett funktionsnamn | HejOrchestrator ( | Namnet på din hållbara funktion |

Du har lagt till en orchestrator för att samordna aktivitetsfunktioner. Öppna *HelloOrchestrator/index.js* för att se orchestrator-funktionen. Varje anrop som `context.df.callActivity` anropar `Hello`en aktivitetsfunktion med namnet .

Därefter lägger du till `Hello` den refererade aktivitetsfunktionen.

### <a name="activity-function"></a>Aktivitetsfunktion

1. Sök efter och välj `Azure Functions: Create Function...`i kommandopaletten .

1. Ange följande information efter anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Aktivitet för varaktiga funktioner | Skapa en aktivitetsfunktion |
    | Ange ett funktionsnamn | Hello | Namnet på din aktivitetsfunktion |

Du har lagt `Hello` till aktivitetsfunktionen som anropas av orchestrator. Öppna *Hello/index.js* om du vill se att det tar ett namn som indata och returnerar en hälsning. En aktivitetsfunktion är där du ska utföra åtgärder som att ringa ett databasanrop eller utföra en beräkning.

Slutligen ska du lägga till en HTTP-utlöst funktion som startar orkestreringen.

### <a name="client-function-http-starter"></a>Klientfunktion (HTTP-start)

1. Sök efter och välj `Azure Functions: Create Function...`i kommandopaletten .

1. Ange följande information efter anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | HTTP-startspelare med varaktiga funktioner | Skapa en HTTP-startfunktion |
    | Ange ett funktionsnamn | Funktioner för varaktiga funktionerhttpstart | Namnet på din aktivitetsfunktion |
    | Auktorisationsnivå | Anonym | I demosyfte kan funktionen anropas utan autentisering |

Du har lagt till en HTTP-utlöst funktion som startar en orkestrering. Öppna *DurableFunctionsHttpStart/index.js* för att `client.startNew` se att den använder för att starta en ny orkestrering. Sedan används `client.createCheckStatusResponse` för att returnera ett HTTP-svar som innehåller webbadresser som kan användas för att övervaka och hantera den nya orkestreringen.

Du har nu en varaktig funktionsapp som kan köras lokalt och distribueras till Azure.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio Code.

1. Om du vill testa funktionen anger `Hello` du en brytpunkt i aktivitetsfunktionskoden (*Hello/index.js*). Tryck på F5 eller välj `Debug: Start Debugging` från kommandopaletten för att starta funktionsappprojektet. Utdata från Core Tools visas på panelen **Terminal**.

    > [!NOTE]
    > Mer information om felsökning finns i diagnostiken för [varaktiga funktioner.](durable-functions-diagnostics.md#debugging)

1. Varaktiga funktioner kräver ett Azure Storage-konto för att köras. När VS-kod uppmanar dig att välja ett lagringskonto väljer du **Välj lagringskonto**.

    ![Skapa lagringskonto](media/quickstart-js-vscode/functions-select-storage.png)

1. Ange följande information för att skapa ett nytt lagringskonto i Azure efter anvisningarna.

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en prenumeration | *namnet på din prenumeration* | Välj din Azure-prenumeration |
    | Välj ett lagringskonto | Skapa ett nytt lagringskonto |  |
    | Ange namnet på det nya lagringskontot | *unikt namn* | Namn på lagringskontot som ska skapas |
    | Välj en resursgrupp | *unikt namn* | Namn på den resursgrupp som ska skapas |
    | Välja en plats | *regionen* | Välj en region nära dig |

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](media/quickstart-js-vscode/functions-f5.png)

1. Med ett verktyg som [Brevbärare](https://www.getpostman.com/) eller [cURL](https://curl.haxx.se/)skickar du en HTTP POST-begäran till URL-slutpunkten. Ersätt det sista segmentet med namnet`HelloOrchestrator`på orchestrator-funktionen ( ). Webbadressen ska likna `http://localhost:7071/api/orchestrators/HelloOrchestrator`.

   Svaret är det första resultatet från HTTP-funktionen så att du vet att den varaktiga orkestreringen har startats. Det är inte ännu slutresultatet av orkestreringen. Svaret innehåller några användbara URL:er. För tillfället kör vi en fråga om orkestreringens status.

1. Kopiera URL-värdet `statusQueryGetUri` för och klistra in det i webbläsarens adressfält och kör begäran. Alternativt kan du också fortsätta att använda Postman för att utfärda GET-begäran.

   Begäran kör en fråga mot orkestreringsinstansen om statusen. Du bör få ett eventuellt svar, vilket visar oss instansen har slutförts, och inkluderar utgångar eller resultat av den varaktiga funktionen. Det ser ut som: 

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

1. Om du vill sluta felsöka trycker du på **Skift + F5** i VS-kod.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Aktivera Kompatibilitetsläge för Azure Functions V2

Samma Azure Functions V2-kompatibilitet som du aktiverade lokalt måste aktiveras i appen i Azure.

1. Med kommandopaletten söker du `Azure Functions: Edit Setting...`efter och väljer .

1. Följ anvisningarna för att hitta din funktionsapp i din Azure-prenumeration.

1. Välj `Create new App Setting...`.

1. Ange en ny `FUNCTIONS_V2_COMPATIBILITY_MODE`inställningsnyckel i .

1. Ange ett inställningsvärde `true`på .

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. Url:en som anropar din HTTP-utlösta funktion bör vara i det här formatet:`http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Du bör få samma statussvar som förut när du använder den publicerade appen.

## <a name="next-steps"></a>Nästa steg

Du har använt Visual Studio Code för att skapa och publicera en beständig JavaScript-funktionsapp.

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)