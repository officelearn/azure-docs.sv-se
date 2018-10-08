---
title: 'Snabbstart: SDK för videosökning i Bing, Node'
titleSuffix: Azure Cognitive Services
description: Konfiguration av konsolprogrammet för SDK för videosökning i Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 4dcc9220d4d38bfe34514edd6a3ad47c7a7d4ba8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225624"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Snabbstart: SDK för videosökning i Bing med Node

SDK:t för videosökning i Bing innehåller funktionerna i REST API:et för videofrågor och parsning av resultat. 

[Källkoden till exemplen med SDK:t för videosökning i Bing med Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) är tillgänglig på Git Hub.

## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med SDK:t för videosökning i Bing kör du `npm install azure-cognitiveservices-videosearch` i din utvecklingsmiljö.

## <a name="video-search-client"></a>Klient för videosökning
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Skapa en instans av `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Instansiera sedan klienten:
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

[Exempel med Cognitive Services SDK för Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
