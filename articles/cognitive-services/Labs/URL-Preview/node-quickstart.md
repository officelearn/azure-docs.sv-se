---
title: 'Snabbstart: Förhandsgranskning av projekt-URL, Node.js'
titlesuffix: Azure Cognitive Services
description: Kom igång med URL-förhandsgranskning i Microsoft Cognitive Services på Azure.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 5c373505cd381108366206c21ff09f25516d7969
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712415"
---
# <a name="quickstart-url-preview-with-nodejs"></a>Snabbstart: URL-förhandsgranskning med Node.js 

Node-exemplet nedan skapar en URL-förhandsgranskning för webbplatsen SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Nödvändiga komponenter

Få en åtkomstnyckel för den kostnadsfria utvärderingsversionen av [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Kodscenario 

Följande kod hämtar data om URL-förhandsgranskning.
De implementeras i följande steg:
1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange fråge-URL för att förhandsgranska, och lägga till frågeparametern.  
3. Skapa en hanterarfunktion för svaret.
4. Definiera sökfunktionen som skapar begäran och lägger till rubriken *Ocp-Apim-Subscription-Key*.
5. Köra sökfunktionen. 

Här följer den fullständiga koden för demon:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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
- [Exempelkod för C#](csharp.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för JavaScript](javascript.md)
- [Snabbstart för Python](python-quickstart.md)
