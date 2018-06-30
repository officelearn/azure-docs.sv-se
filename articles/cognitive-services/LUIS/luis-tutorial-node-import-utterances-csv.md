---
title: Skapa en THOMAS app programmässigt med Node.js | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en THOMAS app programmässigt från befintliga data i CSV-format med hjälp av THOMAS redigering API.
services: cognitive-services
author: DeniseMak
manager: rstand
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: e97dc184266bc9518ee5f909891bd97f7c71804b
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113064"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Skapa en THOMAS app programmässigt med Node.js

THOMAS innehåller ett programmässiga API som allt fungerar som den [THOMAS] [ LUIS] webbplats har. Detta sparar tid när du har befintliga data och det går snabbare att skapa en THOMAS app programmässigt än genom att ange informationen manuellt. 

## <a name="prerequisites"></a>Förutsättningar

* Logga in på den [THOMAS] [ LUIS] webbplats och hitta din [redigering nyckeln](luis-concept-keys.md#authoring-key) i inställningarna för kontot. Du kan använda den här nyckeln för att anropa API: er redigering.
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Den här kursen börjar med en CSV-fil för ett hypotetiskt företags loggfiler användarförfrågningar. Hämta den [här](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Installera den senaste Node.js med NPM. Ladda ner det från [här](https://nodejs.org/en/download/).
* **(Rekommenderas)**  Visual Studio-koden för IntelliSense och felsökning, ladda ner det från [här](https://code.visualstudio.com/) kostnadsfritt.

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mappa befintliga data till avsikter och enheter
Även om du har ett system som inte har skapats med THOMAS i åtanke, om den innehåller textdata som mappas till olika saker användare vill kanske du använder en mappning från de befintliga kategorierna för avsikter i THOMAS indata från användaren. Om du kan identifiera viktiga ord eller fraser i vad användarna sagt, mappa dessa ord till entiteter.

Öppna filen `IoT.csv`. Den innehåller en logg över användarfrågor till en hypotetiska hem automation-tjänsten, inklusive hur de kategoriserat vad användaren SA och vissa kolumner med användbar information som hämtas från dem. 

![CSV-fil](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Du ser som den **RequestType** kolumn kan vara avsikter, och **begära** kolumnen visar ett exempel utterance. De andra fälten kan vara enheter om de sker i utterance. Eftersom det finns avsikter, enheter och exempel utterances kan ha kraven för en enkel, exempelapp.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Steg för att generera en THOMAS app från icke-THOMAS data
Om du vill skapa en ny THOMAS-app från källfilen först analysera data från CSV-fil och konvertera data till ett format som du kan överföra till THOMAS med Authoring-API. Från tolkade data du samla in information om vilka avsikter och enheter är det. Sedan du API-anrop för att skapa appen och Lägg till avsikter och enheter som har samlats in från tolkade data. När du har skapat THOMAS app måste du lägga till exempel utterances från tolkade data. Du kan se detta flöde i den sista delen av följande kod. Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/index.js) denna kod och spara det i `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Parsa CSV

Transaktionerna kolumn som innehåller utterances i CSV-filen har parsas till en JSON-format som THOMAS kan förstå. Den här JSON-format måste innehålla en `intentName` fält som identifierar syftet med utterance. Det måste också innehålla ett `entityLabels` som kan vara tom om det finns inga entiteter i utterance. 

Till exempel på posten ”aktivera indikeringar” mappar till den här JSON:

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

I det här exemplet i `intentName` kommer från användarbegäran under den **begäran** kolumnrubrik i CSV-filen och `entityName` kommer från andra kolumner med viktig information. Om det finns en post för till exempel **åtgärden** eller **enhet**, och att strängen återkommer i den faktiska begäranden sedan den kan är märkt som en entitet. Följande kod visar detta vid parsning av process. Du kan kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_parse.js) det och spara den till `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Skapa THOMAS-app
När data har tolkats till JSON, lägga till en THOMAS app. Följande kod skapar THOMAS appen. Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_create.js) , och spara det i `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Lägg till avsikter
När du har en app kan behöva du avsikter till den. Följande kod skapar THOMAS appen. Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_intents.js) , och spara det i `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Lägg till entiteter
Följande kod lägger till entiteterna THOMAS appen. Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_entities.js) , och spara det i `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Lägg till yttranden
När entiteter och avsikter har definierats i appen THOMAS, kan du lägga till utterances. I följande kod används den [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API, där du kan lägga till upp till 100 utterances i taget.  Kopiera eller [hämta](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_upload.js) , och spara det i `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Kör koden


### <a name="install-nodejs-dependencies"></a>Installera Node.js-beroenden
Installera Node.js-beroenden från NPM i terminal/kommandoraden.

````
> npm install
````

### <a name="change-configuration-settings"></a>Ändra konfigurationsinställningar
För att kunna använda det här programmet måste du ändra värdena i index.js-filen till din egen slutpunktsnyckel, och ange det namn du vill att appen har. Du kan också ange appens kultur eller ändra versionsnumret.

Öppna filen index.js och ändra dessa värden överst i filen.


````JavaScript
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
````
### <a name="run-the-script"></a>Kör skriptet
Kör skriptet från en terminal/kommandorad med Node.js.

````
> node index.js
````
eller
````
> npm start
````

### <a name="application-progress"></a>Programmet pågår
Medan programmet körs kommandoraden visar förloppet. Kommandoradens utdata innehåller svar från THOMAS format.

````
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
````




## <a name="open-the-luis-app"></a>Öppna appen THOMAS
När skriptet har slutförts, du kan logga in till [THOMAS] [ LUIS] och se THOMAS appen som du skapade under **Mina appar**. Du ska kunna se utterances som du lagt till under den **aktivera**, **avstängning**, och **ingen** avsikter.

![Aktivera avsikt](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa och träna din app i THOMAS webbplats](interactive-test.md)

## <a name="additional-resources"></a>Ytterligare resurser

Det här exempelprogrammet använder följande LUIS APIs:
- [Skapa app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Lägg till avsikter](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [lägga till enheter](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Lägg till utterances](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

