---
title: Entiteten Sök SDK nod quickstart | Microsoft Docs
description: Installationsprogrammet för entiteten Sök SDK-konsolprogram.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2904ecfed33334458f9b6a9ca2500cd0bfef13bc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355737"
---
# <a name="entity-search-sdk-node-quickstart"></a>Entiteten Sök SDK nod Snabbstart

Bing enheten Sök SDK innehåller funktionerna i REST-API för entiteten frågor och resultaten för tolkning. 

Den [källkoden för C# Bing enheten Sök SDK-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) är tillgänglig på Git-hubben.
## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med Bing enheten Sök SDK kör `npm install azure-cognitiveservices-entitysearch` i din utvecklingsmiljö.

## <a name="entity-search-client"></a>Entiteten Sök klienten
Hämta en [kognitiva åtkomstnyckeln](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Skapa en instans av den `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Sedan skapa en instans av klienten och söka efter resultat:
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
Kod som skrivs ut `result.value` objekt till konsolen utan att tolka all text.  Resultaten, om någon per kategori, kommer att omfatta:
- _skriv: 'Sak'
- _skriv: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Nästa steg

[Kognitiva services SDK för Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)