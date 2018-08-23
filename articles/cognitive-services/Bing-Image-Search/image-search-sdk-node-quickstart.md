---
title: 'Snabbstart: Begär och filtrera bilder med hjälp av SDK: N i Node.js'
description: I den här snabbstarten du begär och filtrera bilder som returneras av bildsökning i Bing, med hjälp av Node.js.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e88c045b220192a617e6b8caf5d8d53f70a25b5e
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987516"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-nodejs"></a>Snabbstart: Begär och filtrera bilder med hjälp av SDK och Node.js

Sök SDK för Bing-bild innehåller funktionerna i REST-API för bild-frågor och parsa resultat. 

Den [källkoden för noden Bing bild Search SDK-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) är tillgänglig på Git Hub.

## <a name="application-dependencies"></a>Programberoenden

Om du vill konfigurera ett konsolprogram med Search SDK för Bing-avbildningen kör `npm install azure-cognitiveservices-imagesearch` i din utvecklingsmiljö.

## <a name="image-search-client"></a>Sök-klienten för avbildning
Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under *Search*. Skapa en instans av den `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Sedan kan skapa en instans av klienten:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Du kan använda klienten för att söka med en frågetext, i det här fallet ”El Capitan”:
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

[Cognitive services SDK för Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)