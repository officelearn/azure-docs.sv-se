---
title: 'Snabbstart: Söka efter bilder – SDK för bildsökning i Bing för Node.js'
description: Använd den här snabbstarten till att göra din första bildsökning med API för bildsökning i Bing, som är en adapterklass för API:t och innehåller samma funktioner. Den här enkla Node.js-appen skickar en bildsökningsfråga, parsar JSON-svaret och visar webbadressen till den första bild som returneras.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0858ebdd283ea6696f145eabe2ef923d98f73601
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508059"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-nodejs"></a>Snabbstart: Söka efter bilder med SDK för bildsökning i Bing för Node.js

Använd den här snabbstarten till att göra din första bildsökning med API för bildsökning i Bing, som är en adapterklass för API:t och innehåller samma funktioner. Den här enkla JavaScript-appen skickar en bildsökningsfråga, parsar JSON-svaret och visar webbadressen till den första bild som returneras.

Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) tillsammans med ytterligare felhantering och kommentarer.

## <a name="prerequisites"></a>Nödvändiga komponenter
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under **Sök**.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

* [Cognitive Services Image Search SDK för Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Installera med `npm install azure-cognitiveservices-imagesearch`
* [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure)-modulen
    * Installera med `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i den IDE eller redigerare du föredrar och ange skyddstyp, https och andra krav.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. Skapa variabler för en giltig prenumerationsnyckel, bildresultatet som ska returneras av Bing och en sökterm i main-metoden i projektet. Sedan skapa en instans av bildsökningsklienten med hjälp av nyckeln.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Skapa en asynkron hjälpfunktion

1. Skapa en funktion som anropar klienten asynkront och returnerar svaret från tjänsten Bildsökning i Bing.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>Skicka en fråga och hantera svaret

1. Anropa hjälpfunktionen och hantera dess `promise` så att det returnerade bildresultatet parsas.

    Om svaret innehåller sökresultat ska du lagra och skriva ut information om det första resultatet, som en webbadress till miniatyrbilden, en webbadress till originalet och det totala antalet returnerade bilder.  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie om enkel app för bildsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Se också

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en interaktiv demo online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Hämta en kostnadsfri åtkomstnyckel för Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Node.js-exempel för Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [API-referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
