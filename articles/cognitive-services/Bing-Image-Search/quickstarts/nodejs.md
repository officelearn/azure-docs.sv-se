---
title: 'Snabbstart: Skicka sökfrågor med sökning i Bing med hjälp av Node.js'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att söka efter bilder på webben med hjälp av Bing Web Search API.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 43f0cfec6aa2d4263b6a627736c2a432b2943145
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576654"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-rest-api-and-nodejs"></a>Snabbstart: Skicka sökfrågor med Bings API för bildsökning REST och Node.js

Använd den här snabbstarten att skicka ditt första anrop till sökning i Bing och få ett JSON-svar. Det här enkla JavaScript-programmet skickar en sökfråga till API: et och visar rådataresultat.

Även om det här programmet är skriven i JavaScript och körs i Node.js API: et är tjänsten en RESTful-webb-kompatibla de flesta programmeringsspråk.

Källkoden för det här exemplet finns [på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) med ytterligare felhantering och kod anteckningar.

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* Den [Library för JavaScript-begäran](https://github.com/request/request)
[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera programmet

1. Skapa en ny JavaScript-fil i din favorit-IDE eller redigerare och ställa in kraven på Skyddstyperna och https.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Skapa variablerna för den API-slutpunkten, API för bildhantering sökvägen, din prenumerationsnyckel och Sök efter termer.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruera sökbegäran och fråga.

1. Använda variabler i det sista steget för att formatera en URL för API-begäran. Observera att sökordet måste vara URL-kodat innan den skickas till API: et.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Använd begäran-biblioteket för att skicka din fråga till API: et. `response_handler` definieras i nästa avsnitt.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Hantera och parsa svaret

1. Definiera en funktion med namnet `response_handler` som tar ett HTTP-anrop `response`, som en parameter. Utför följande steg i den här funktionen:
    
    1. Definiera en variabel så att den innehåller brödtexten i JSON-svar.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Store brödtexten i svaret när den **data** flaggan kallas 
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. När en **slutet** flaggan stoppsignal JSON kan bearbetas och URL för bilden kan skrivas ut, tillsammans med det totala antalet returnerade avbildningar.
    
        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="json-response"></a>JSON-svar

Svar från den bildsökning i Bing returneras som JSON. Den här exempelsvaret har trunkerats för att visa ett enskilt resultat.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bildsökning i Bing ensidesapp självstudien](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se också 

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en online Interaktiv demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Hämta en kostnadsfri Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)