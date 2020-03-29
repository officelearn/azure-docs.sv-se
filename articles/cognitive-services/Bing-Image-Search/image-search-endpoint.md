---
title: Slutpunkter för API:et för bing-bildsökning
titleSuffix: Azure Cognitive Services
description: Api:et för bildsökning innehåller tre slutpunkter. Slutpunkt 1 returnerar bilder från webben. Slutpunkt 2 returnerar ImageInsights. Slutpunkt 3 returnerar populära bilder.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072629"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Slutpunkter för API:et för bing-bildsökning

**Api:et för bildsökning** innehåller tre slutpunkter.  Slutpunkt 1 returnerar bilder från webben baserat på en fråga. Slutpunkt 2 [returnerar ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Slutpunkt 3 returnerar populära bilder.

## <a name="endpoints"></a>Slutpunkter

Om du vill hämta bildresultat med hjälp av Bing API skickar du en begäran till en av följande slutpunkter. Använd rubrikerna och URL-parametrarna för att definiera ytterligare specifikationer.

**Slutpunkt 1:** Returnerar bilder som är relevanta för användarens sökfråga som definieras av `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Slutpunkt 2:** Returnerar insikter om en `GET` bild `POST`med antingen eller .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
En GET-begäran returnerar insikter om en bild, till exempel webbsidor som innehåller bilden. Inkludera parametern [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) med en `GET` begäran.

Du kan också inkludera en binär bild `POST` i brödtexten för `RecognizedEntities`en begäran och ställa in [parametern moduler](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) till . Detta returnerar en [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) att använda `GET` som en parameter i en efterföljande begäran, som returnerar information om personer i bilden.  Ställ `modules` `All` in på att få `RecognizedEntities` alla insikter, `POST` utom i resultatet `insightsToken`av utan att göra ett annat samtal med hjälp av .


**Slutpunkt 3:** Returnerar bilder som trendar baserat på sökförfrågningar som gjorts av andra. Bilderna är uppdelade i olika kategorier, till exempel baserat på anmärkningsvärda personer eller händelser.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

En lista över marknader som stöder populära bilder finns i [Populära bilder](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Mer information om rubriker, parametrar, marknadskoder, svarsobjekt, fel osv., se [Bing Image Search API v7-referensen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
## <a name="response-json"></a>Svar JSON
Svaret på en begäran om bildsökning innehåller resultat som JSON-objekt. Exempel på tolkning av resultaten finns [i självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) och [källkoden](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Nästa steg
Bing-API:erna stöder sökåtgärder som returnerar resultat efter typ. **Bing**Alla sökslutpunkter returnerar resultat som JSON-svarsobjekt. Alla slutpunkter stöder frågor som returnerar ett visst språk och/eller en viss plats med longitud, latitud och sökradie.

Fullständig information om de parametrar som stöds av varje slutpunkt finns i referenssidorna för varje typ.
Exempel på grundläggande begäranden med hjälp av api:et för bildsökning finns i [Snabbstartar för bildsökning](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
