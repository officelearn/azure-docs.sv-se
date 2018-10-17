---
title: 'Snabbstart: Utföra en bildsökning med Node.js – API för bildsökning i Bing'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att skicka ditt första anrop till API:et för bildsökning i Bing och få ett JSON-svar. Det här enkla JavaScript-programmet skickar en sökfråga till API:et och visar rådataresultatet.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 1584b3e0a1e1c560d42b5f8320d0e15ad6242918
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294602"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-rest-api-and-nodejs"></a>Snabbstart: Skicka sökfrågor med REST API för bildsökning i Bing och Node.js

Använd den här snabbstarten för att skicka ditt första anrop till API:et för bildsökning i Bing och få ett JSON-svar. Det här enkla JavaScript-programmet skickar en sökfråga till API:et och visar rådataresultatet.

Även om det här programmet är skrivet i JavaScript och körs i Node.js, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) tillsammans med ytterligare kommentarer om hantering av fel och kodanteckningar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* [Begäransbiblioteket för JavaScript](https://github.com/request/request)
[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i din favorit-IDE eller -redigerare och ange strikthet och https-krav.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Skapa variabler för API-slutpunkten, sökväg för bild-API, din prenumerationsnyckel och sökord.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruera sökbegäran och fråga.

1. Använd variablerna från det sista steget för att formatera en sök-URL för API-begäran. Observera att sökordet måste vara URL-kodat innan det skickas till API:et.

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

2. Använd begäransbiblioteket för att skicka din fråga till API:et. `response_handler` definieras i nästa avsnitt.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Hantera och parsa svaret

1. Definiera en funktion med namnet `response_handler` som tar ett HTTP-anrop, `response`, som en parameter. Utför följande steg inom den här funktionen:

    1. Definiera en variabel så att den innehåller brödtexten i JSON-svaret.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Lagra svarets brödtext när flaggan för **data** anropas.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. När en flagga för **slut** signaleras kan JSON bearbetas och bildens URL kan skrivas ut tillsammans med det totala antalet returnerade bilder.

        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="json-response"></a>JSON-svar

Svar från API för bildsökning i Bing returneras som JSON. Det här exempelsvaret har trunkerats för att visa ett enskilt resultat.

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
> [Självstudie om enkel app för bildsökning i Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se även

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en interaktiv demo online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Hämta en kostnadsfri åtkomstnyckel för Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [API-referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
