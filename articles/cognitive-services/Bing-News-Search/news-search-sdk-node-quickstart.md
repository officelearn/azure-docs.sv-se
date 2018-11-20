---
title: 'Snabbstart: SDK för nyhetssökning i Bing, Node'
titleSuffix: Azure Cognitive Services
description: Konfigurera konsolprogrammet för SDK för nyhetssökning i Bing
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: bc168cf696d6280ce4c0e7cb46f90af4a2ad7aa0
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686505"
---
# <a name="quickstart-bing-news-search-sdk-with-node"></a>Snabbstart: SDK för nyhetssökning i Bing med Node

SDK för nyhetssökning i Bing innehåller funktionerna i REST API:et för nyhetsfrågor och parsning av resultat. 

[Källkoden till exemplen med SDK:t för nyhetssökning i Bing med Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) är tillgänglig på Git Hub.

## <a name="application-dependencies"></a>Programberoenden

Så här skapar du ett konsolprogram med API för nyhetssökning i Bing:
* Kör `npm install ms-rest-azure` i utvecklingsmiljön.
* Kör `npm install azure-cognitiveservices-newssearch` i utvecklingsmiljön.

## <a name="news-search-client"></a>Klient för nyhetssökning
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Skapa en instans av `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Instansiera sedan klienten:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Använd klienten för att söka med frågetext, i det här fallet ”Winter Olympics”:
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Koden skriver ut `result.value`-objekt till konsolen utan parsning av texten. I resultaten, om sådana finns för respektive kategori, ingår:
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Nästa steg

[Exempel med Cognitive Services SDK för Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
