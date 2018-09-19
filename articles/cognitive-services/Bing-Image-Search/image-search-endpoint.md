---
title: Bild Sök slutpunkter - bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över tillgängliga slutpunkter för Bings API för bildsökning.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: ca38943908bf3eee04c40cf4decf81fd20b08a1f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295928"
---
# <a name="image-search-endpoints"></a>Bild Sök slutpunkter

Den **Image Search API** innehåller tre slutpunkter.  Slutpunkt 1 returnerar bilder från webben baserat på en fråga. Slutpunkt 2 returnerar [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse).  Slutpunkten 3 returnerar populära bilder.
## <a name="endpoints"></a>Slutpunkter
För att få avbildningen resultat med hjälp av Bing-API kan du skicka en begäran till någon av följande slutpunkter. Använda rubriker och URL-parametrar för att definiera ytterligare specifikationer.

**Slutpunkt 1:** returnerar avbildningar som är relevanta för användarens sökfråga som definieras av `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Slutpunkt 2:** returnerar insikter om en avbildning med hjälp av antingen `GET` eller `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
En GET-begäran returnerar insikter om en avbildning, till exempel webbsidor med avbildningen. Inkludera den [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parameter med en `GET` begäran.

Eller, du kan inkludera en binär i brödtexten i en `POST` begära och ange den [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parameter `RecognizedEntities`. Detta returnerar en [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) ska användas som en parameter i ett efterföljande `GET` begäran, vilket returnerar information om personer i avbildningen.  Ange `modules` till `All` för att få alla insikter, förutom `RecognizedEntities` i resultatet av den `POST` utan att göra en annan anrop med den `insightsToken`.


**Slutpunkt 3:** returnerar avbildningar som är populära baserat på search-begäranden som görs av andra. Bilderna är indelade i olika kategorier, till exempel baserat på anmärkningsvärda personer eller händelser.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

En lista över marknader som har stöd för populära bilder, se [populära bilder](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [bildsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) referens.
## <a name="response-json"></a>Svaret JSON
Svaret på en förfrågan för avbildningen search inkluderar resultat som JSON-objekt. Exempel för att tolka resultaten finns i den [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) och [källkod](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom att longitud, latitud och Sök radius.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med API för bildsökning finns [bildsökning Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
