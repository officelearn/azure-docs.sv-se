---
title: Slut punkter för API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: 'Bildsökning-API: et innehåller tre slut punkter. Slut punkt 1 returnerar bilder från webben. Slut punkt 2 returnerar ImageInsights. Slut punkt 3 returnerar trend bilder.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 4ed48a46a01cee5a7a981d4e863f981010b44112
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084263"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Slut punkter för API för bildsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

**Bildsökning-API: et** innehåller tre slut punkter.  Slut punkt 1 returnerar bilder från webben baserat på en fråga. Slut punkt 2 returnerar [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Slut punkt 3 returnerar trend bilder.

## <a name="endpoints"></a>Slutpunkter

Skicka en begäran till någon av följande slut punkter för att hämta avbildnings resultat med Bing-API: et. Använd sidhuvudena och URL-parametrarna för att definiera ytterligare specifikationer.

**Slut punkt 1:** Returnerar bilder som är relevanta för användarens Sök fråga som definieras av `?q=""` .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Slut punkt 2:** Returnerar insikter om en bild med antingen `GET` eller `POST` .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
En GET-begäran returnerar insikter om en bild, till exempel webb sidor som innehåller bilden. Inkludera parametern [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) med en `GET` begäran.

Du kan också inkludera en binär bild i bröd texten i en `POST` begäran och ange parametern [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) till `RecognizedEntities` . Detta returnerar en [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) som kan användas som en parameter i en efterföljande `GET` begäran, som returnerar information om personer i avbildningen.  Ställ in `modules` på `All` för att hämta alla insikter, förutom `RecognizedEntities` i resultaten av `POST` utan något annat anrop med hjälp av `insightsToken` .


**Slut punkt 3:** Returnerar bilder som är trender baserat på Sök begär Anden som andra har gjort. Bilderna är indelade i olika kategorier, till exempel baserat på intressanta personer eller evenemang.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

En lista över marknader som stöder Trends images finns i [Trends bilder](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Mer information om sidhuvuden, parametrar, marknads koder, svars objekt, fel osv. finns i referens för [API för bildsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) .
## <a name="response-json"></a>Svars-JSON
Svaret på en avbildnings Sök förfrågan innehåller resultat som JSON-objekt. Exempel på hur du tolkar resultaten finns i [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) och [käll koden](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Nästa steg
API: erna för **Bing** stöder Sök åtgärder som returnerar resultat enligt deras typ. Alla Sök slut punkter returnerar resultat som JSON-svars objekt.  Alla slut punkter har stöd för frågor som returnerar ett särskilt språk och/eller plats efter longitud, latitud och Sök-radie.

Fullständig information om de parametrar som stöds av varje slut punkt finns i referens sidorna för varje typ.
Exempel på grundläggande förfrågningar som använder API: et för bilds ökning finns [bildsökning snabb starter](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
