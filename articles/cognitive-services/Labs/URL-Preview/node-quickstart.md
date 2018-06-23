---
title: Node.js Snabbstart för Project URL Preview - kognitiva Microsoft-tjänster | Microsoft Docs
description: Komma igång med URL-förhandsgranskning i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 195033d2740b11873baae095cec028dc8d19ce49
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354012"
---
# <a name="url-preview-node-quickstart"></a>URL: en förhandsgranskning nod Snabbstart

Noden med följande exempel skapas en förhandsgranskning för URL: en för webbplatsen SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Förutsättningar

Hämta en åtkomstnyckel för den kostnadsfria utvärderingsversionen [kognitiva Services Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Koden scenario 

Följande kod hämtar URL förhandsgranska data.
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

````

## <a name="next-steps"></a>Nästa steg
- [C#-kodexempel](csharp.md)
- [Java-Snabbstart](java-quickstart.md)
- [JavaScript-Snabbstart](javascript.md)
- [Python-Snabbstart](python-quickstart.md)