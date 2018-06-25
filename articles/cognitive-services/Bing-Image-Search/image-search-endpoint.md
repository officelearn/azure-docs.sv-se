---
title: Bild Sök slutpunkter | Microsoft Docs
description: Sammanfattning av avbildningen Sök API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: 0d5f28bfdb45b27b04df068f75e8a20d0235d12b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351399"
---
# <a name="image-search-endpoints"></a>Bild Sök slutpunkter
Den **avbildningen Sök API** innehåller tre slutpunkter.  Slutpunkt 1 returnerar bilder från webben baserat på en fråga. Slutpunkt 2 returnerar [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse).  Slutpunkten 3 returnerar trender bilder.
## <a name="endpoints"></a>Slutpunkter
Skicka en begäran till någon av följande slutpunkter för att hämta bildresultat med hjälp av Bing-API. Använda sidhuvuden och URL-parametrar för att definiera ytterligare specifikationer.

**Slutpunkt 1:** returnerar avbildningar som är relevanta för användarens sökfråga definieras av `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Slutpunkt 2:** returnerar information om en avbildning med hjälp av antingen `GET` eller `POST`.
``` 
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
En GET-begäran returnerar information om en avbildning, till exempel webbsidor som innehåller bilden. Inkludera den [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parameter med en `GET` begäran.

Eller så kan du inkludera en binär i brödtexten för en `POST` begära och ange den [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) parameter till `RecognizedEntities`. Detta returnerar en [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken) ska användas som en parameter i en efterföljande `GET` begäran, som returnerar information om personer i avbildningen.  Ange `modules` till `All` för att få alla insikter, förutom `RecognizedEntities` i resultaten av den `POST` utan att göra en annan anrop med hjälp av den `insightsToken`. 


**Slutpunkt 3:** returnerar avbildningar som trender utifrån search-begäranden som görs av andra. Bilderna är indelade i olika kategorier, till exempel utifrån anmärkningsvärda personer eller händelser.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

En lista över marknader som stöder trender avbildningar, se [trender bilder](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Mer information om huvuden, parametrar, marknaden koder, svar objekt, fel, etc., se den [Bing avbildningen Sök API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) referens.
## <a name="response-json"></a>Svaret JSON
Svaret på en sökbegäran bild innehåller resultatet som JSON-objekt. Exempel för att tolka resultaten finns i [kursen](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) och [källkod](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökning åtgärder som returnerar enligt deras typ. Alla Sök slutpunkter returnerar resultat som objekt för JSON-svar.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom longitud, latitud och RADIUS-sökning.

Fullständig information om de parametrar som stöds av varje slutpunkt finns på referenssidor för varje typ av.
Exempel på grundläggande begäranden med hjälp av sökfunktionen API för avbildning finns [Image-sökning Quick-startar](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).