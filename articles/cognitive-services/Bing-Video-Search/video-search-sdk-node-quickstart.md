---
title: Video Sök SDK nod quickstart | Microsoft Docs
description: Installationsprogrammet för Video Sök SDK-konsolprogram.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 5718c750288e0a5605db3296d2911cca5e03375c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355749"
---
# <a name="video-search-sdk-node-quickstart"></a>Video Sök SDK nod Snabbstart

Bing Video Sök SDK innehåller funktionerna i REST-API för video frågor och resultaten för tolkning. 

Den [källkoden för noden Bing Video Sök SDK-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) är tillgänglig på Git-hubben.

## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med Bing Video Sök SDK kör `npm install azure-cognitiveservices-videosearch` i din utvecklingsmiljö.

## <a name="video-search-client"></a>Video Sök-klient
Hämta en [kognitiva åtkomstnyckeln](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Skapa en instans av den `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Sedan skapa en instans av klienten:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Sök efter resultat.
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Nästa steg

[Kognitiva services SDK för Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
