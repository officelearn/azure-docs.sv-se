---
title: Importera yttranden med hjälp av Node.js
titleSuffix: Azure
description: Lär dig hur du skapar en LUIS-app via programmering från befintliga data i CSV-format med hjälp av redigering LUIS-API.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: diberry
ms.openlocfilehash: da638064b2ead1cd860f3b4f96ffa88026aab4ff
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101204"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Skapa en LUIS-app via programmering med hjälp av Node.js

LUIS ger en programmatisk API som gör allt som den [LUIS](luis-reference-regions.md) webbplats har. Det här kan spara tid när du har befintliga data och det skulle vara snabbare att skapa en LUIS-app via programmering än genom att ange informationen manuellt. 

## <a name="prerequisites"></a>Förutsättningar

* Logga in på den [LUIS](luis-reference-regions.md) webbplats och hitta din [redigering nyckeln](luis-concept-keys.md#authoring-key) i inställningarna för kontot. Du kan använda den här nyckeln för att anropa API: er redigering.
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Den här självstudien utgår från en CSV-fil för ett hypotetiskt företag loggfiler för användarbegäranden. Ladda ned den [här](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Installera den senaste Node.js med NPM. Ladda ned det från [här](https://nodejs.org/en/download/).
* **(Rekommenderas)**  Visual Studio Code för IntelliSense och felsökning kan du hämta det från [här](https://code.visualstudio.com/) utan kostnad.

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mappa befintliga data till avsikter och entiteter
Även om du har ett system som inte har skapats med LUIS i åtanke, om den innehåller textdata som mappas till olika saker användare vill göra kanske du kan få fram en mappning från de befintliga kategorierna av användarens indata till avsikter i LUIS. Om du kan identifiera viktiga ord eller fraser i vad användarna sägs, mappa dessa ord till entiteter.

Öppna filen `IoT.csv`. Den innehåller en logg över användarfrågor ett hypotetiskt home automation-tjänsten, inklusive hur de har kategoriserat vad användaren sägs och vissa kolumner med användbar information som hämtas från dem. 

![CSV-filen med befintliga data](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Ser du att den **RequestType** kolumn kan vara avsikter, och **begära** kolumnen visar en exempel-uttryck. I andra fält kan vara entiteter om de sker i uttryck. Eftersom det finns avsikter och entiteter exempel yttranden kan ha kraven för en enkel, exempelapp.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Steg för att skapa en LUIS-app från icke-LUIS-data
Om du vill generera en ny LUIS-app från källfilen först parsa data från CSV-filen och omvandla dessa data till ett format som du kan överföra till LUIS med hjälp av API för redigering. Från den tolkade data du samla in information om vilka avsikter och entiteter är det inte. Sedan du göra API-anrop för att skapa appen och Lägg till avsikter och entiteter som har samlats in från tolkade data. När du har skapat LUIS-app, kan du lägga till exempel yttranden från tolkade data. Du kan se det här flödet i den sista delen av följande kod. Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/index.js) denna kod och spara den i `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Parsa CSV

Kolumnposter som innehåller yttranden i CSV-filen måste parsas till en JSON-format som LUIS kan förstå. Det här JSON-format måste innehålla en `intentName` fält som identifierar syftet med uttryck. Måste den också innehålla en `entityLabels` fält som kan vara tom om det finns inga entiteter i uttryck. 

Till exempel posten för ”tänd lamporna” mappas till JSON:

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

I det här exemplet på `intentName` kommer från användarförfrågan under den **begäran** kolumnrubrik i CSV-filen och `entityName` kommer från de andra kolumnerna med viktig information. Exempel: om det finns en post för **åtgärden** eller **enhet**, och att strängen återkommer i den faktiska begäran och sedan den kan är märkt som en entitet. Följande kod visar detta vid parsning av process. Du kan kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_parse.js) den och spara den i `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Skapa LUIS-app
När data har tolkats till JSON, lägga till en LUIS-app. Följande kod skapar LUIS-app. Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_create.js) , och spara den i `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Lägg till avsikter
När du har en app kan behöva du avsikter till den. Följande kod skapar LUIS-app. Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_intents.js) , och spara den i `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Lägg till entiteter
Följande kod lägger till entiteterna LUIS-app. Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_entities.js) , och spara den i `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Lägg till yttranden
När de entiteter och avsikter har definierats i LUIS-app, kan du lägga till talade. I följande kod används den [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API där du kan lägga till upp till 100 yttranden i taget.  Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_upload.js) , och spara den i `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Kör koden


### <a name="install-nodejs-dependencies"></a>Installera Node.js-beroenden
Installera Node.js-beroenden från NPM i terminal/kommandoraden.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Ändra konfigurationsinställningarna
För att kunna använda det här programmet, måste du ändra värdena i filen index.js till dina egna slutpunktsnyckeln och ange det namn som du vill att appen ska ha. Du kan också ange appens kultur eller ändra versionsnumret.

Öppna filen index.js och ändra dessa värden överst i filen.


```nodejs
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Kör skriptet
Kör skriptet från en terminal/kommandoraden med Node.js.

```console
> node index.js
```

eller

```console
> npm start
```

### <a name="application-progress"></a>Program pågår
När programmet körs kommandoraden visar förloppet. Utdata från kommandoraden innehåller formatet på svar från LUIS.

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




## <a name="open-the-luis-app"></a>Öppna LUIS-app
När skriptet har slutförts kan du logga in på [LUIS](luis-reference-regions.md) och se LUIS-app som du skapade under **Mina appar**. Du bör kunna se yttranden som du har lagt till under den **aktivera**, **avstängning**, och **ingen** avsikter.

![Aktivera avsikt](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa och träna din app i LUIS-webbplats](luis-interactive-test.md)

## <a name="additional-resources"></a>Ytterligare resurser

Det här exempelprogrammet använder följande LUIS APIs:
- [Skapa app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Lägg till avsikter](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Lägg till entiteter](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Lägg till yttranden](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)