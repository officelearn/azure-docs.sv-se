---
title: 'Snabbstart: Kontrollera stavning med REST API för stavningskontroll i Bing och Node.js'
titlesuffix: Azure Cognitive Services
description: Kom igång med REST API för stavningskontroll i Bing för att kontrollera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: 0a1260de6428f6ebc70757261cdcc3002820ec7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066292"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Snabbstart: Kontrollera stavning med REST API för stavningskontroll i Bing och Node.js

Använd den här snabbstarten för att göra ditt första anrop till REST API för stavningskontroll i Bing. Den här enkla noden programmet skickar en begäran till API: et och returnerar en lista med ord som inte känner till, följt av föreslagna ändringar. Det här programmet är skriven i Node.js är API: et en RESTful-webb-tjänst som är kompatibla med de flesta programmeringsspråk. Källkoden till det här programmet finns [på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Node.js 6](https://nodejs.org/en/download/) eller senare.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa en ny JavaScript-fil i valfri IDE eller valfritt redigeringsprogram. Ange Skyddstyperna och kräver `https`. Skapa sedan variabler för API-slutpunktens värd, sökväg och prenumerationsnyckel.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Skapa variabler för sökparametrarna och den text som du vill kontrollera. Lägg till marknaden koden efter `mkt=`. Koden marknad är det land som du gör begäran från. Dessutom lägga till din stavningskontroll läge efter `&mode=`. Läget är antingen `proof` (fångar de flesta/stavning och grammatik-fel) eller `spell` (fångar de flesta stavning men inte så många grammatikfel).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Skapa begärandeparametrar

Skapa dina begärandeparametrar genom att skapa ett nytt objekt med en `POST`-metod. Lägg till sökvägen genom att lägga till slutpunktssökvägen och frågesträngen. Lägg till din prenumerationsnyckel i `Ocp-Apim-Subscription-Key`-huvudet.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Skapa en svarshanterare

Skapa en funktion som heter `response_handler` för att ta JSON-svaret från API:et och skriva ut det. Skapa en variabel för svarstexten. Lägg till svaret när en `data`-flagga tas emot med hjälp av `response.on()`. När en `end`-flagga tas emot skriver du ut JSON-brödtexten till konsolen.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>Skicka begäran

Anropa API:et med hjälp av `https.request()` med begärandeparametrarna samt svarshanteraren. Skriv texten till API:et och avsluta begäran efteråt.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](../tutorials/spellcheck.md)

- [Vad är API:et för stavningskontroll i Bing?](../overview.md)
- [API-referens för stavningskontroll i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
