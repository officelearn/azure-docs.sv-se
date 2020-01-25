---
title: Hämta modell med REST-anrop iC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: a262db04e51015edb760a8b04952dfa24b2ad63a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748879"
---
## <a name="prerequisites"></a>Krav

* Start nyckel.
* Importera [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) -appen från kognitiva-tjänsterna – språkförståelseing GitHub-lagringsplatsen.
* LUIS program-ID för den importerade TravelAgent-appen. Program-ID visas på programmets instrumentpanel.
* Versions-ID: t i programmet som tar emot yttranden. Standard-id:t är ”0.1”.
* Programmeringsspråket [Node.js](https://nodejs.org/) 
* [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Ändra modell program mässigt

Använd Go för att lägga till en enhets- [API](https://aka.ms/luis-apim-v3-authoring) som har registrerats av enheten i programmet. 

1. Skapa en ny fil med namnet `model.js`. Lägg till följande kod:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    
    const LUIS_authoringKey = "YOUR-KEY";
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;
    
    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];
    
    const main = async() =>{
    
    
        await addUtterance();
        await train("POST");
        await trainStatus("GET");
    
    }
    const addUtterance = async () => {
    
        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {
    
        const options = {
            uri: addTrainURI,
            method: verb, 
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    
    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```
1. Ersätt följande värden:

    * `YOUR-KEY` med din start nyckel
    * `YOUR-ENDPOINT` med slut punkten, till exempel `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` med appens ID

1. Med en kommando tolk i samma katalog som den plats där du skapade filen, anger du följande kommando för att köra filen:

    ```console
    node model.js
    ```  

## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort filen från fil systemet. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips för en app](../luis-concept-best-practices.md)