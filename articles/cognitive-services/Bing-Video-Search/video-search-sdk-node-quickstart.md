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
ms.openlocfilehash: 985ddcff35a16c747fff34ed487c72744e1ee466
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313460"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Snabbstart: SDK för videosökning i Bing med Node

SDK:t för videosökning i Bing innehåller funktionerna i REST API:et för videofrågor och parsning av resultat. 

[Källkoden till exemplen med SDK:t för videosökning i Bing med Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) är tillgänglig på Git Hub.

## <a name="application-dependencies"></a>Programberoenden
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under **Sök**.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Så här skapar du ett konsolprogram med API för videosökning i Bing:
* Kör `npm install ms-rest-azure` i utvecklingsmiljön.
* Kör `npm install azure-cognitiveservices-videosearch` i utvecklingsmiljön.

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
