---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.author: diberry
ms.openlocfilehash: 2cf75056cd045822e083a15f7590bf020db8cd5e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425269"
---
Använd klient biblioteket Language Understanding (LUIS) för att Node.js till:

* Förutsägelse per plats
* Förutsägelse efter version

[Referens dokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime)  |  [Runtime-paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js)

## <a name="prerequisites"></a>Förutsättningar

* Language Understanding runtime-resurs: [skapa en i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Ett LUIS app-ID – Använd ID: t för den offentliga IoT-appen `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . Den användar fråga som används i snabb starts koden är speciell för den appen.

## <a name="setting-up"></a>Konfigurera

### <a name="get-your-language-understanding-luis-runtime-key"></a>Hämta din Language Understanding (LUIS) körnings nyckel

Hämta din [körnings nyckel](../luis-how-to-azure-subscription.md) genom att skapa en Luis runtime-resurs. Behåll din nyckel och slut punkten för nyckeln för nästa steg.

### <a name="create-a-new-javascript-nodejs-file"></a>Skapa en ny JavaScript-fil (Node.js)

Skapa en ny JavaScript-fil i önskat redigerings program eller IDE, med namnet `luis_prediction.js` .

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Installera NPM-biblioteket för LUIS runtime

I program katalogen installerar du beroendena med följande kommando:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objekt modell

Redigerings klienten för Language Understanding (LUIS) är ett [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) -objekt som autentiserar till Azure, som innehåller din redigerings nyckel.

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* [Förutsägelse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) efter `staging` eller `production` plats
* [Förutsägelse efter version](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med klient biblioteket Language Understanding (LUIS) förutsägelse körning:

* [Förutsägelse per plats](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du `luis_prediction.js` filen i önskat redigerings program eller IDE. Lägg till följande beroenden:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autentisera klienten

1. Skapa variabler för din egen obligatoriska LUIS-information:

    Lägg till variabler för att hantera din förutsägelse nyckel och slut punkts nyckel. 
    
    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Variables)]

1. Skapa en variabel för app-ID: t med namnet `LUIS_APP_ID` . Ange variabeln till den offentliga IoT-appen **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Skapa en variabel för att ange den `production` publicerade platsen.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=OtherVariables)]


1. Skapa ett msRest. ApiKeyCredentials-objekt med din nyckel och Använd slut punkten för att skapa en [Luis. LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) -objekt.

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Hämta förutsägelse från körning

Lägg till följande metod för att skapa begäran till förutsägelse körning.

Användaren uttryck är en del av [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) -objektet.

Metoden **[luisRuntimeClient. förutsägelse. getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** kräver flera parametrar, till exempel App-ID, plats namn och objektet förutsägelse begär Ande för att uppfylla begäran. De andra alternativen, till exempel verbose, Visa alla avsikter och loggen är valfria.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Huvud kod för förutsägelsen

Använd följande huvudsakliga metod för att koppla ihop variablerna och metoderna för att hämta förutsägelsen.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node luis_prediction.js` kommandot från program katalogen.

```console
node luis_prediction.js
```

Förutsägelse resultatet returnerar ett JSON-objekt:

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

När du är klar med dina förutsägelser kan du rensa arbetet från den här snabb starten genom att ta bort filen och dess under kataloger.
