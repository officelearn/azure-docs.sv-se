---
title: 'Snabbstart: Project Answer Search, Node'
description: Kom igång med Project Answer Search med Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 1afd029803fc7d2709a9a9abe840db6d7f52498d
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465754"
---
# <a name="quickstart-project-answer-search-with-node"></a>Snabbstart: Project Answer Search med Node

I följande exempel med Node skapas en fråga för information om Yosemite National Park.

## <a name="prerequisites"></a>Nödvändiga komponenter

Få en åtkomstnyckel för den kostnadsfria utvärderingsversionen av [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

I det här exemplet används Node v8.9.4

## <a name="code-scenario"></a>Kodscenario 

Följande kod hämtar svar.
De implementeras i följande steg:
1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange fråge-URL för att förhandsgranska, och lägga till frågeparametern.  
3. Skapa en hanterarfunktion för svaret.
4. Definiera sökfunktionen som skapar begäran och lägger till rubriken *Ocp-Apim-Subscription-Key*.
5. Köra sökfunktionen. 

Här följer den fullständiga koden för demon:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

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

````

## <a name="next-steps"></a>Nästa steg
- [Exempelkod för C#](c-sharp-quickstart.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)