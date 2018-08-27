---
title: 'Snabbstart: Använd Bing-webbsökning SDK för Node.js'
description: Konfigurera för webbsökning SDK-konsolprogram.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: e25c295fc0fc144110325d3c494a513ea35aeb05
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888598"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Snabbstart: Använd Bing-webbsökning SDK för Node.js

Bing Web Search SDK innehåller funktionen för REST-API för webbfrågor och parsa resultat.

Den [källkoden för noden Bing Web Search SDK-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) finns på GitHub.

## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med Bing Web Search SDK kör `npm install azure-cognitiveservices-websearch` i din utvecklingsmiljö.

## <a name="web-search-client"></a>Sök webbklienten
Hämta en [Cognitive Services prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Search*. Skapa en instans av den `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Sedan kan skapa en instans av klienten:
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
Sök efter resultat:
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
Koden skriver ut `result.value` objekt till konsolen utan parsning text.  I resultaten, om sådant per kategori, inkluderas:
- _typ: 'ImageObject'
- _typ: 'NewsArticle'
- _typ: ”webbsida”
- _typ: 'VideoObjectElementType'

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Nästa steg

[Cognitive services SDK för Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
