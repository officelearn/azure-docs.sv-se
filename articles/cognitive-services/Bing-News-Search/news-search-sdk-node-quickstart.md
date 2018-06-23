---
title: Nyheter Sök SDK nod quickstart | Microsoft Docs
description: Ställ in nyheter Sök SDK-konsolprogram
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 4ae99aa100b697a0dd75863c6f0c3c556dfa3d21
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355284"
---
# <a name="news-search-sdk-node-quickstart"></a>Nyheter Sök SDK nod Snabbstart

Bing News Sök SDK innehåller funktionerna i REST-API för Nyheter frågor och resultaten för tolkning. 

Den [källkoden för noden Bing News Sök SDK-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) är tillgänglig på Git-hubben.

## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med Bing News Sök SDK kör `npm install azure-cognitiveservices-newssearch` i din utvecklingsmiljö.

## <a name="news-search-client"></a>Nyheter Sök klienten
Hämta en [kognitiva åtkomstnyckeln](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Skapa en instans av den `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Sedan skapa en instans av klienten:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Använd klienten för att söka med en frågetext, i det här fallet 'Vinter olympiad':
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Kod som skrivs ut `result.value` objekt till konsolen utan att tolka all text. Resultaten, om någon per kategori, kommer att omfatta:
- _skriv: 'NewsArticle'
- _skriv: 'Webbsida ”
- _skriv: 'VideoObject'
- _skriv: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Nästa steg

[Kognitiva services SDK för Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
