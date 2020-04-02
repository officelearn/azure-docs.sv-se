---
title: Snabbstart för JavaScript-klientbibliotek för Visuell sökning
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: 404529bf269f899603b92c6c23b0d95cd2749ee5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550025"
---
Använd den här snabbstarten för att börja hämta bildinsikter från tjänsten Bing Visual Search med hjälp av JavaScript-klientbiblioteket. Bing Visual Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-visualsearch/?view=azure-node-latest) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch) | [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch) | [Exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Krav
* [Node.js](https://www.nodejs.org/)
* Klientbiblioteket Bing Visual Search för JavaScript
    * Om du vill konfigurera ett konsolprogram med hjälp av klientbiblioteket Bing Visual Search kör du följande kommandon:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i valfri IDE eller redigeringsprogram och lägg till följande krav. Skapa sedan variabler för din prenumerationsnyckel, ID för anpassad konfiguration och filsökväg för den bild du vill ladda upp. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Instantiera klienten.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Sök efter bilder

1. Använd `fs.createReadStream()` för att läsa in bildfilen och skapa variabler för sökbegäran och resultat. Använd sedan klienten till att söka efter bilder.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Parsa resultaten från den föregående frågan:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../../tutorial-bing-visual-search-single-page-app.md)
