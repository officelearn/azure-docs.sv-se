---
title: Importera yttranden med Node.js - LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en LUIS-app programmässigt från befintliga data i CSV-format med hjälp av LUIS Authoring API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ef5f6967b7ad9500672d00d93dd8acaca99e5948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499466"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Skapa en LUIS-app programmässigt med Node.js

LUIS tillhandahåller ett programmatiskt API som gör allt som [LUIS-webbplatsen](luis-reference-regions.md) gör. Detta kan spara tid när du har redan existerande data och det skulle vara snabbare att skapa en LUIS-app programmässigt än genom att ange information för hand. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Krav

* Logga in på [LUIS-webbplatsen](luis-reference-regions.md) och hitta [din redigeringsnyckel](luis-concept-keys.md#authoring-key) i Kontoinställningar. Du använder den här nyckeln för att anropa skribentens api:er.
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.
* Den här artikeln börjar med en CSV för ett hypotetiskt företags loggfiler för användarbegäranden. Ladda ner den [här](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Installera den senaste Node.js med NPM. Ladda ned det [här](https://nodejs.org/en/download/).
* **[Rekommenderas]** Visual Studio Kod för IntelliSense och felsökning, ladda ner den [härifrån](https://code.visualstudio.com/) gratis.

All kod i den här artikeln är tillgänglig i [GitHub-databasen för språkförståelse i Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv). 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mappa befintliga data till avsikter och entiteter
Även om du har ett system som inte har skapats med LUIS i åtanke, om det innehåller textdata som mappar till olika saker som användare vill göra, kanske du kan komma med en mappning från de befintliga kategorierna av användarindata till avsikter i LUIS. Om du kan identifiera viktiga ord eller fraser i vad användarna sa, kan dessa ord mappas till entiteter.

Öppna [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) filen. Den innehåller en logg över användarfrågor till en hypotetisk hemautomatiseringstjänst, inklusive hur de kategoriserades, vad användaren sa och några kolumner med användbar information som dras ut ur dem. 

![CSV-fil med redan existerande data](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Du ser att kolumnen **RequestType** kan vara avsikter och kolumnen **Begäran** visar ett exempel yttrande. De andra fälten kan vara entiteter om de förekommer i uttrycket. Eftersom det finns avsikter, entiteter och exempelyttranden har du kraven för en enkel exempelapp.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Steg för att generera en LUIS-app från data som inte är LUIS
Så här skapar du en ny LUIS-app från CSV-filen:

* Tolka data från CSV-filen:
    * Konvertera till ett format som du kan överföra till LUIS med hjälp av redigerings-API:et. 
    * Samla in information om avsikter och entiteter från tolkade data. 
* Skapa API-anrop till:
    * Skapa appen.
    * Lägg till avsikter och entiteter som har samlats in från tolkade data. 
    * När du har skapat LUIS-appen kan du lägga till exempelyttranden från tolkade data. 

Du kan se det här programflödet `index.js` i den sista delen av filen. Kopiera eller [ladda ner](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) den `index.js`här koden och spara den i .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Tolka CSV

Kolumnposterna som innehåller yttrandena i CSV måste tolkas i ett JSON-format som LUIS kan förstå. Det här JSON-formatet måste innehålla ett `intentName` fält som identifierar avsikten med uttrycket. Det måste också `entityLabels` innehålla ett fält som kan vara tomt om det inte finns några entiteter i uttrycket. 

Posten för "Slå på lamporna" mappar till den här JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

I det här `intentName` exemplet kommer från användarbegäran under kolumnrubriken **Begäran** `entityName` i CSV-filen och kommer från de andra kolumnerna med nyckelinformation. Om det till exempel finns en post för **Operation** eller **Enhet**och strängen också förekommer i den faktiska begäran, kan den märkas som en entitet. Följande kod visar den här tolkningsprocessen. Du kan kopiera eller [ladda](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) `_parse.js`ner den och spara den på .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Skapa LUIS-appen
När data har tolkats i JSON lägger du till dem i en LUIS-app. Följande kod skapar LUIS-appen. Kopiera eller [ladda ner](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) den, och spara den i `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Lägg till avsikter
När du har en app måste du ha avsikter. Följande kod skapar LUIS-appen. Kopiera eller [ladda ner](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) den, och spara den i `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Lägg till entiteter
Följande kod lägger till entiteterna i LUIS-appen. Kopiera eller [ladda ner](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) den, och spara den i `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Lägg till yttranden
När entiteterna och avsikterna har definierats i LUIS-appen kan du lägga till yttranden. Följande kod använder [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API, vilket gör att du kan lägga till upp till 100 yttranden åt gången.  Kopiera eller [ladda ner](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) den, och spara den i `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Kör koden


### <a name="install-nodejs-dependencies"></a>Installera nod.js-beroenden
Installera nod.js-beroenden från NPM på terminal-/kommandoraden.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Ändra konfigurationsinställningar
För att kunna använda det här programmet måste du ändra värdena i index.js-filen till din egen slutpunktsnyckel och ange det namn du vill att appen ska ha. Du kan också ställa in appens kultur eller ändra versionsnumret.

Öppna filen index.js och ändra dessa värden högst upp i filen.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Kör skriptet
Kör skriptet från en terminal-/kommandorad med Node.js.

```console
> node index.js
```

eller

```console
> npm start
```

### <a name="application-progress"></a>Förloppet för programmet
Medan programmet körs visar kommandoraden förloppet. Kommandoradsutdata innehåller formatet för svaren från LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Öppna LUIS-appen
När skriptet är klart kan du logga in på [LUIS](luis-reference-regions.md) och se LUIS-appen som du skapade under **Mina appar**. Du bör kunna se de yttranden som du har lagt till under avsikterna **TurnOn,** **TurnOff**och **Ingen.**

![TurnOn-avsikt](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa och träna din app på LUIS webbplats](luis-interactive-test.md)

## <a name="additional-resources"></a>Ytterligare resurser

Det här exempelprogrammet använder följande LUIS API:er:
- [skapa app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [lägga till avsikter](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [lägga till entiteter](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [lägga till yttranden](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
