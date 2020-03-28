---
title: Snabbstart - Skicka en fråga till API:et med Node.js - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att börja skicka begäranden till API:et för sökning i Bing lokalt företag, som är en Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: beab594126ce292ea1fc47e399a12274dbb31aa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665703"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Snabbstart: Skicka en fråga till API:et för lokal företagssökning i Bing med Node.js

Använd den här snabbstarten för att börja skicka begäranden till API:et för sökning i Bing lokalt företag, som är en Azure Cognitive Service. Även om detta enkla program är skrivet i Node.js, api är en RESTful webbtjänst kompatibel med alla programmeringsspråk som kan göra HTTP-förfrågningar och tolka JSON.

Det här exemplet programmet hämtar lokala svarsdata `hotel in Bellevue`från API för sökfrågan .

## <a name="prerequisites"></a>Krav

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* [Begäransbiblioteket för JavaScript](https://github.com/request/request)

Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med API:er för Bing. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Använd åtkomstnyckeln som tillhandahålls av den kostnadsfria utvärderingsversionen.  Se även Priser för Cognitive Services –API för Bing-sökning.

## <a name="code-scenario"></a>Kodscenario

Följande kod får definierar och skickar begäran. De implementeras i följande steg:

1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange frågan och lägg till frågeparametern.
3. Skapa en hanterarfunktion för svaret.
4. Definiera den sökfunktion som skapar begäran och lägger till nyckeln Ocp-Apim-Subscription-Key.
5. Köra sökfunktionen.

Här följer den fullständiga koden för demon:

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

* [Snabbstart för lokal företagssökning](local-quickstart.md)
* [Snabbstart för Java-sökning för lokal företagssökning](local-search-java-quickstart.md)
* [Snabbstart för Lokal företagssökning i Python](local-search-python-quickstart.md)
