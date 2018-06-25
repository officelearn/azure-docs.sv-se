---
title: Video Sök slutpunkter | Microsoft Docs
description: Sammanfattning av Video Sök API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-gedod
ms.openlocfilehash: 9836d9928362ab37b0a81ff5043d99f9bf353f22
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351498"
---
# <a name="video-search-endpoints"></a>Video Sök-slutpunkter
Den **Video Sök API** innehåller tre slutpunkter.  Slutpunkt 1 returnerar videor från webben baserat på en fråga. Slutpunkt 2 returnerar inblick i en video baserat på den `modules` URL-parametern.  Slutpunkten 3 returnerar trender bilder.

## <a name="endpoints"></a>Slutpunkter
För att få video resultat med hjälp av Bing-API kan skicka en `GET` begäran till någon av följande slutpunkter. Använda sidhuvuden och URL-parametrar för att definiera ytterligare specifikationer.

**Slutpunkt 1:** returnerar videor som är relevanta för användarens sökfråga definieras av `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Slutpunkt 2:** returnerar inblick i en video, till exempel relaterade videor. Inkludera den `modules` [Frågeparametern](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), vilket är en kommaavgränsad lista över insikter om begäran.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Slutpunkt 3:** returnerar videor som trender utifrån search-begäranden som görs av andra. Videor är indelade i olika kategorier, till exempel utifrån anmärkningsvärda personer eller händelser.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Mer information om huvuden, parametrar, marknaden koder, svar objekt, fel, etc., se den [Bing Video Sök API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) referens.
## <a name="response-json"></a>Svaret JSON
Svaret på en sökbegäran videor innehåller resultatet som JSON-objekt. Exempel för att tolka resultaten finns i [kursen](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) och [källkod](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökning åtgärder som returnerar enligt deras typ. Alla Sök slutpunkter returnerar resultat som objekt för JSON-svar.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom longitud, latitud och RADIUS-sökning.

Fullständig information om de parametrar som stöds av varje slutpunkt finns på referenssidor för varje typ av.
Exempel på grundläggande begäranden med hjälp av Video sökfunktionen API finns [Sök Video Quick-startar](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).