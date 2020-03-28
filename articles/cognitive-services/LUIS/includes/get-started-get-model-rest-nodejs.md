---
title: 'Skaffa modell med REST-samtal i C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: bbb2ae0b10af795d71f0a78c045bec0c216ee378
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368405"
---
## <a name="prerequisites"></a>Krav

* Azure Language Understanding - Författare resurs 32 teckennyckel och redigering slutpunkt URL. Skapa med [Azure-portalen](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) eller [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importera [TravelAgent-appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) från GitHub-databasen för kognitiva tjänster-språkförståelse.
* App-id:t i LUIS för den importerade TravelAgent-appen. Program-ID visas på programmets instrumentpanel.
* Den versions-ID i programmet som tar emot yttrandena. Standard-id:t är ”0.1”.
* Programmeringsspråket [Node.js](https://nodejs.org/)
* [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Ändra modell programmässigt

1. Skapa en ny fil med namnet `model.js`. Lägg till följande kod:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');

    // 32 character key value
    const LUIS_authoringKey = "YOUR-KEY";

    // endpoint example: your-resource-name.api.cognitive.microsoft.com
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

1. Ersätt de värden `YOUR-` som börjar med dina egna värden.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din 32 tecken författande nyckel.|
    |`YOUR-ENDPOINT`| Slutpunkten för redigerings-URL. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`. Du anger resursnamnet när du skapade resursen.|
    |`YOUR-APP-ID`| Ditt LUIS-app-ID. |

    Tilldelade nycklar och resurser visas i LUIS-portalen i avsnittet Hantera på sidan **Azure-resurser.** App-ID:et är tillgängligt i samma avsnittet Hantera på sidan **Programinställningar.**

1. Med en kommandotolk i samma katalog som när du skapade filen anger du följande kommando för att köra filen:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstarten tar du bort filen från filsystemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för en app](../luis-concept-best-practices.md)