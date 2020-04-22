---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 9c15e4217c5331346c5a95329bae7e2a4f0e0841
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732084"
---
Använd klientbiblioteket Språk understanding (LUIS) för Node.js för att:

* Förutsägelse efter kortplats
* Förutsägelse efter version

[Referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Körningspaket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Krav

* Körningsresurs för språk understanding: [Skapa en i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Ett LUIS-app-ID – använd det offentliga `df67dcdb-c37d-46af-88e1-8b97951ca1c2`IoT-app-ID:et för . Användarfrågan som används i snabbstartskoden är specifik för den appen.

## <a name="setting-up"></a>Inrätta

### <a name="get-your-language-understanding-luis-runtime-key"></a>Få din körtidsnyckel (Language Understanding)

Hämta [din körningsnyckel](../luis-how-to-azure-subscription.md) genom att skapa en LUIS-körningsresurs. Behåll nyckeln och slutpunkten för nyckeln för nästa steg.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Skapa en ny JavaScript-fil (Node.js)

Skapa en ny JavaScript-fil i önskad `luis_prediction.js`redigerare eller IDE med namnet .

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Installera NPM-biblioteket för LUIS-körningen

Installera beroendena med följande kommando i programkatalogen:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objektmodell

LUIS-redigeringsklienten (Language Understanding) är ett [LUISAuthoringClient-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) som autentiserar till Azure, som innehåller din redigeringsnyckel.

När klienten har skapats använder du den här klienten för att komma åt funktioner, inklusive:

* [Förutsägelse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) `staging` av `production` eller kortplats
* [Förutsägelse per version](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket för förutsägelsekörning (Language Understanding) (LUIS):

* [Förutsägelse efter kortplats](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna `luis_prediction.js` filen i önskad redigerare eller IDE från projektkatalogen. Lägg till följande beroenden:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autentisera klienten

1. Skapa variabler för din egen nödvändiga LUIS-information:

    Lägg till variabler för att hantera förutsägelsenyckeln som hämtats från en miljövariabel med namnet `LUIS_RUNTIME_KEY`. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE: t eller shell som kör den stängas och laddas om för att komma åt variabeln. Metoderna skapas senare.

    Skapa en variabel för `LUIS_RUNTIME_ENDPOINT`att hålla ditt resursnamn .

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Skapa en variabel för app-ID:t som en miljövariabel med namnet `LUIS_APP_ID`. Ange miljövariabeln till den offentliga **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** IoT-appen . Skapa en variabel `production` för att ange den publicerade platsen.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Skapa ett msRest.ApiKeyCredentials-objekt med nyckeln och använd det med slutpunkten för att skapa en [LUIS. LUISRuntimeClient-objekt.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Få förutsägelse från körning

Lägg till följande metod för att skapa begäran i förutsägelsekörningen.

Användaren yttrande är en del av [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) objektet.

**[LuisRuntimeClient.prediction.getSlotPrediction-metoden](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** behöver flera parametrar, till exempel app-ID, platsnamnet och förutsägelsebegäran för att uppfylla begäran. De andra alternativen, till exempel utförlig, visa alla avsikter och logg är valfria.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Huvudkoden för förutsägelsen

Använd följande huvudmetod för att binda variablerna och metoderna tillsammans för att få förutsägelsen.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `node luis_prediction.js` kommandot från programkatalogen.

```console
node luis_prediction.js
```

Förutsägelseresultatet returnerar ett JSON-objekt:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med dina förutsägelser rensar du arbetet från den här snabbstarten genom att ta bort filen och dess underkataloger.
