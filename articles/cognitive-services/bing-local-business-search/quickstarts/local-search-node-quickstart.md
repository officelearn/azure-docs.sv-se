---
title: 'Snabb start – skicka en fråga till API: et med Node. js – Bing lokal företags sökning'
titleSuffix: Azure Cognitive Services
description: 'Använd den här snabb starten för att börja skicka begär anden till API: et för lokal sökning i Bing, som är en Azure-tjänst för inlärning.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3bb31c36e8c614a72b86f95cb7e7d1c588692f97
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873094"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Snabb start: skicka en fråga till Bing Local Business Search API med Node. js

Använd den här snabb starten för att lära dig hur du skickar begär anden till API: et för lokal sökning i Bing, som är en Azure-tjänst för inlärning. Även om det här enkla programmet skrivs i Node. js, är API: et en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

Det här exempel programmet hämtar lokala svars data från API: et för en Sök fråga.

## <a name="prerequisites"></a>Krav

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).
* [Java Script Request-biblioteket](https://github.com/request/request).
* Ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med API:er för Bing-sökresultat. I den här snabb starten räcker den [kostnads fria utvärderings versionen](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) . Spara API-nyckeln som anges när du aktiverar din kostnads fria utvärderings version. Mer information finns i [Cognitive Services priser – Bing-sökning API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="code-scenario"></a>Kodscenario

Följande kod definierar och skickar begäran, som implementeras i följande steg:

1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange frågan och Lägg till Frågeparametern.
3. Skapa en hanterarfunktion för svaret.
4. Definiera Sök funktionen som skapar begäran och lägger till `Ocp-Apim-Subscription-Key` rubriken.
5. Kör Sök funktionen.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Nästa steg

* [Snabb start för lokal affärs sökning i C#](local-quickstart.md)
* [Lokal affärs sökning Java snabb start](local-search-java-quickstart.md)
* [Snabb start för att söka i lokalt företag](local-search-python-quickstart.md)
