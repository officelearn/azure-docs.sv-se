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
ms.openlocfilehash: 1f2a5f6a1473cde40928ada6e30f6bd9b780543d
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814890"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Snabbstart: SDK för entitetssökning i Bing med Node

SDK för entitetssökning i Bing innehåller funktionerna i REST API:et för entitetsfrågor och parsning av resultat. 

[Källkoden till exemplen med SDK:t för entitetssökning i Bing med C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) är tillgänglig på Git Hub.
## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med SDK:t för entitetssökning i Bing kör du `npm install azure-cognitiveservices-entitysearch` i din utvecklingsmiljö.

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