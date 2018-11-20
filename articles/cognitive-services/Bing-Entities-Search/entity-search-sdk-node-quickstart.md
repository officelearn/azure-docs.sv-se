---
title: 'Snabbstart: SDK för entitetssökning i Bing, Node'
titleSuffix: Azure Cognitive Services
description: Konfigurera för SDK för entitetssökning-konsolprogrammet med Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 69bcca7871d9bf4bebf64c0c0ae1b54cd8408927
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684128"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Snabbstart: SDK för entitetssökning i Bing med Node

SDK för entitetssökning i Bing innehåller funktionerna i REST API:et för entitetsfrågor och parsning av resultat. 

[Källkoden till exemplen med SDK:t för entitetssökning i Bing med C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) är tillgänglig på GitHub.
## <a name="application-dependencies"></a>Programberoenden

Så här skapar du ett konsolprogram med API för entitetssökning i Bing:
* Kör `npm install ms-rest-azure` i utvecklingsmiljön.
* Kör `npm install azure-cognitiveservices-entitysearch` i utvecklingsmiljön.

## <a name="entity-search-client"></a>Klient för entitetssökning
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Skapa en instans av `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Skapa sedan en instans av klienten och sök efter resultat:
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
Koden skriver ut `result.value`-objekt till konsolen utan parsning av texten.  I resultaten, om sådana finns för respektive kategori, ingår:
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Nästa steg

[Exempel med Cognitive Services SDK för Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)