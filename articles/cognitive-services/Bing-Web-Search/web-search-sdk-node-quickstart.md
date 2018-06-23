---
title: Webbsökning SDK nod quickstart | Microsoft Docs
description: Installationsprogrammet för webbsökning SDK-konsolprogram.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355299"
---
# <a name="web-search-sdk-node-quickstart"></a>Snabbstart för Web Sök SDK nod

Bing Web Sök SDK innehåller funktionerna i REST API för webb-frågor och tolkning resultat.

Den [källkoden för noden Bing Web Sök SDK-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) är tillgänglig på Git-hubben.

## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med Bing Web Sök SDK kör `npm install azure-cognitiveservices-websearch` i din utvecklingsmiljö.

## <a name="web-search-client"></a>Sök Webbklient
Hämta en [kognitiva åtkomstnyckeln](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Skapa en instans av den `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Sedan skapa en instans av klienten:
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
Kod som skrivs ut `result.value` objekt till konsolen utan att tolka all text.  Resultaten, om någon per kategori, kommer att omfatta:
- _skriv: 'ImageObject'
- _skriv: 'NewsArticle'
- _skriv: 'Webbsida ”
- _skriv: 'VideoObjectElementType'

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Nästa steg

[Kognitiva services SDK för Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
