---
title: 'Snabb start – skicka en fråga till API: et för lokal sökning i Bing med Node. js'
titleSuffix: Azure Cognitive Services
description: Börja använda Bing-API för lokal affärs sökning i noden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: f5025c71a16626c714c4256cbc1d52d655a71a45
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906270"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Snabbstart: Skicka en fråga till Bing Local Business Search API med Node. js

Använd den här snabb starten för att börja skicka begär anden till API: et för lokal sökning i Bing, som är en Azure-tjänst för inlärning. Även om det här enkla programmet skrivs i Node. js är API: et en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

Det här exempel programmet hämtar lokala svars data från API: et för `hotel in Bellevue`Sök frågan.

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* [Begäransbiblioteket för JavaScript](https://github.com/request/request)

Du måste ha ett [COGNITIVE Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med Bing-API: er. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Använd den åtkomst nyckel som tillhandahålls av den kostnads fria utvärderings versionen.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Kodscenario

Följande kod definierar och skickar begäran. Den implementeras i följande steg:

1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange frågan och Lägg till Frågeparametern.
3. Skapa en hanterarfunktion för svaret.
4. Definiera Sök funktionen som skapar begäran och lägger till rubriken OCP-APIM-Subscription-Key.
5. Köra sökfunktionen.

Här följer den fullständiga koden för demon:

```
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

* [Snabb start för lokal affärs sökning](local-quickstart.md)
* [Lokal affärs sökning Java snabb start](local-search-java-quickstart.md)
* [Snabb start för att söka i lokalt företag](local-search-python-quickstart.md)
