---
title: 'Snabbstart: API för entitetssökning i Bing, Node.js'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API:et för entitetssökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: aahi
ms.openlocfilehash: 80af035d3d3f4c530e417669c2fa9acad39eb355
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52315995"
---
# <a name="quickstart-for-bing-entity-search-api-with-nodejs"></a>Snabbstart för API för entitetssökning i Bing med Node.js

Den här artikeln visar hur du använder [API:et för entitetssökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)  med Node.JS.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Node.js 6](https://nodejs.org/en/download/) för att köra den här koden.

Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API:et för entitetssökning i Bing**. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) för den här snabbstarten. Du behöver den åtkomstnyckel som du fick när du aktiverade din kostnadsfria utvärderingsversion, eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="search-entities"></a>Entitetssökning

Följ dessa steg för att köra programmet.

1. Skapa ett nytt Node.js-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/entities';

let mkt = 'en-US';
let q = 'italian restaurant near me';

let params = '?mkt=' + mkt + '&q=' + encodeURI(q);

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.end ();
}

Search ();
```

**Svar**

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
        "url": "http://www.princi.com/",
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

[Överst på sidan](#HOLTop)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie om entitetssökning i Bing](../tutorial-bing-entities-search-single-page-app.md)
> [Översikt över entitetssökning i Bing](../search-the-web.md )
> [API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
