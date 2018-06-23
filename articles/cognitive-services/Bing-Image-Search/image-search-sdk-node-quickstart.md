---
title: Image-sökning SDK nod quickstart | Microsoft Docs
description: Installationsprogrammet för Image-sökning SDK-konsolprogram.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e4c8303e39accbb7caec15c0ef47d701971ce632
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355254"
---
# <a name="image-search-sdk-node-quickstart"></a>Bild Sök SDK nod Snabbstart

Bing avbildningen Sök SDK innehåller funktionerna i REST-API för avbildningen frågor och resultaten för tolkning. 

Den [källkoden för noden Bing avbildningen Sök SDK-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) är tillgänglig på Git-hubben.

## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med Bing avbildningen Sök SDK kör `npm install azure-cognitiveservices-imagesearch` i din utvecklingsmiljö.

## <a name="image-search-client"></a>Klienten för avbildning sökning
Hämta en [kognitiva åtkomstnyckeln](https://azure.microsoft.com/try/cognitive-services/) under *Sök*. Skapa en instans av den `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Sedan skapa en instans av klienten:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Använd klienten för att söka med en frågetext, i det här fallet ”El Capitan':
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>Nästa steg

[Kognitiva services SDK för Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)