---
title: 'Snabbstart: Sök efter bilder med hjälp av SDK för Bing Search-avbildningen och Node.js'
description: I den här snabbstarten du begär och filtrera bilder som returneras av bildsökning i Bing, med hjälp av Node.js.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cagronlund
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: ebe6629344c076119c0bfdaee76a69df6274ca28
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45572591"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-nodejs"></a>Snabbstart: Sök efter bilder med SDK för Bing Search-avbildningen och Node.js

Använd den här snabbstarten för att göra sökningen första avbildning med hjälp av Bing-bild Search SDK, som är en Omslutning för API: et och innehåller samma funktioner. Det här enkla JavaScript-programmet skickar en sökfråga för avbildningen, Parsar JSON-svar och visar Webbadressen till den första bilden som returneras.

Källkoden för det här exemplet är tillgänglig på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) med ytterligare felhantering och anteckningar.

## <a name="prerequisites"></a>Förutsättningar

* Den [Cognitive Services bild Search SDK för Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Installera med hjälp av `npm install azure-cognitiveservices-imagesearch`
* Den [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure) modul
    * Installera med hjälp av `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera programmet

1. Skapa en ny JavaScript-fil i din favorit-IDE eller redigerare och ange Skyddstyperna, https och andra krav.

    ```javascript
    'use strict';
    https = require('https');
    const Search = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. Skapa variabler för prenumerationsnyckeln giltig bild resultatet som returneras av Bing och en sökterm i ditt projekt main-metoden. Sedan skapa en instans av avbildningen search klienten med hjälp av nyckeln.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request 
    let searchTerm = "canadian rockies";

    //instantiate the the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new Search.ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Skapa en asynkron hjälpfunktionen

1. Skapa en funktion för att anropa klienten asynkront och returnerar svaret från tjänsten bildsökning i Bing.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
## <a name="send-a-query-and-handle-the-response"></a>Skicka en fråga och hantera svaret 

1. Anropa hjälpfunktionen och hantera dess `promise` att parsa bild resultatet som returneras i svaret.

    Om svaret innehåller sökresultat, lagra det första resultatet och skriva ut information om, till exempel en miniatyrbild URL, den ursprungliga URL: en, tillsammans med det totala antalet returnerade avbildningar.  
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
> [Bildsökning i Bing ensidesapp självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Se också 

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en online Interaktiv demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Hämta en kostnadsfri Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) 
* [Node.js-exempel för Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)