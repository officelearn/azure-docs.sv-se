---
title: Noden Snabbstart för Microsoft kognitiva Services, projekt svaret Sök | Microsoft Docs
description: Komma igång med projektet svaret sökning, kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353973"
---
# <a name="project-answer-search-node-quickstart"></a>Projektet svaret Sök nod Snabbstart

I följande exempel nod skapar en fråga om information om Yosemite National Park.

## <a name="prerequisites"></a>Förutsättningar

Hämta en åtkomstnyckel för den kostnadsfria utvärderingsversionen [kognitiva Services Labs](https://aka.ms/answersearchsubscription)

Det här exemplet används noden v8.9.4

## <a name="code-scenario"></a>Koden scenario 

Följande kod hämtar svar.
De är implementerade i följande steg:
1. Deklarera variabler för att ange slutpunkten av värd och sökväg.
2. Ange fråge-URL för att förhandsgranska och Lägg till Frågeparametern.  
3. Skapa en hanterarfunktion för svaret.
4. Definiera sökfunktionen som skapar begäran och lägger till den *Ocp-Apim-prenumeration-nyckeln* huvud.
5. Kör sökfunktionen. 

Den fullständiga koden för den här demon följande:

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
- [C#-kodexempel](c-sharp-quickstart.md)
- [Java-Snabbstart](java-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)