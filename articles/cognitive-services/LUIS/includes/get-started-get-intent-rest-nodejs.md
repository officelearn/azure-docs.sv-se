---
title: Hämta avsikt med REST-anrop i Node. js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 3bef3cbb321465893b3a05242f0d72d77d091d6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733329"
---
## <a name="prerequisites"></a>Krav

* Programmeringsspråket [Node.js](https://nodejs.org/)
* [Visual Studio-koden](https://code.visualstudio.com/)
* Ett LUIS app-ID – Använd ID: t för `df67dcdb-c37d-46af-88e1-8b97951ca1c2`den offentliga IoT-appen. Den användar fråga som används i snabb starts koden är speciell för den appen.

## <a name="create-luis-runtime-key-for-predictions"></a>Skapa LUIS runtime Key för förutsägelser

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Klicka på [skapa **language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Ange alla nödvändiga inställningar för **körnings** nyckel:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Plats|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0`– den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs sidan.
1. Samla in `endpoint` konfigurerade och `key`a.

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt

Använd Node. js för att fråga efter [förutsägelse slut punkten](https://aka.ms/luis-apim-v3-prediction) och få ett förutsägelse resultat.

1. Kopiera följande kodfragment till en fil med namnet `predict.js`:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');

    // Analyze text
    //
    getPrediction = async () => {

        // YOUR-KEY - Language Understanding runtime key
        var endpointKey = "YOUR-KEY";

        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";

        // Set the LUIS_APP_ID environment variable
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

        var utterance = "turn on all lights";

        // Create query string
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }

        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`

        // HTTP Request
        const response = await requestpromise(URI);

        // HTTP Response
        console.log(response);

    }

    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Ersätt värdena `YOUR-KEY` och `YOUR-ENDPOINT` med din egen **körnings** nyckel och slut punkt för förutsägelse.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din 32-teckenuppsättning med **körnings** nyckel.|
    |`YOUR-ENDPOINT`| URL-slutpunkten för förutsägelse. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Installera `request`-, `request-promise`-och `querystring` -beroenden med det här kommandot:

    ```console
    npm install request request-promise querystring
    ```

1. Kör appen med det här kommandot:

    ```console
    node predict.js
    ```

 1. Granska det förutsägelse svar som returneras som JSON:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    JSON-svaret formaterat för läsbarhet:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
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

När du är färdig med den här snabb starten tar du bort filen från fil systemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till yttranden och träna](../get-started-get-model-rest-apis.md)