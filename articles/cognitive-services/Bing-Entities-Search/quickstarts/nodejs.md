---
title: 'Snabb start: skicka en Sök förfrågan till REST API med Node. js-Entitetssökning i Bing'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten om du vill skicka en begäran till REST API för entitetssökning i Bing med hjälp av C# och få ett JSON-svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 194368acd6be65da6a800ad1394ac156a6654b50
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650239"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Snabb start: skicka en Sök förfrågan till Entitetssökning i Bing REST API med Node. js

Använd den här snabbstarten för att göra ditt första anrop till API för entitetssökning i Bing och visa JSON-svaret. Det här enkla JavaScript-programmet skickar en nyhetssökfråga till API:et och visar svaret. Käll koden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Även om det här programmet är skrivet i Java Script är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Krav

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* [Java Script Request-biblioteket](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i din favorit-IDE eller-redigerare och ange sträng-och HTTPS-krav.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Skapa variabler för API-slutpunkten, prenumerationsnyckeln och sökfrågan. Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Lägg till parametrarna för marknad och fråga till en sträng som heter `query`. Se till att URL-koda frågan med `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Hantera och parsa svaret

1. Definiera en funktion med namnet `response_handler()` som tar ett HTTP-anrop, `response`, som parameter. 

2. I den här funktionen definierar du en variabel som innehåller bröd texten i JSON-svaret.  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Lagra bröd texten i svaret när `data` flaggan anropas.
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. När en `end` flagga signalerar, tolkar du JSON och skriver ut den.

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
        ```

## Send a request

1. Create a function called `Search()` to send a search request. In it, perform the following steps:

2. Within this function, create a JSON object containing your request parameters. Use `Get` for the method, and add your host and path information. Add your subscription key to the `Ocp-Apim-Subscription-Key` header. 

3. Use `https.request()` to send the request with the response handler created previously, and your search parameters.
    
   ```javascript
   let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + query,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    
    let req = https.request (request_params, response_handler);
    req.end ();
   }
      ```

2. Anropa funktionen `Search()`.

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API för entitetsökning i Bing?](../overview.md )
* [API för entitetsökning i Bing referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
