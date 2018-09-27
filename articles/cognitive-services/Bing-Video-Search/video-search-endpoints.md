---
title: Videosökning slutpunkter – Videosökning i Bing
titlesuffix: Azure Cognitive Services
description: Sammanfattning av Videosökning i Bing-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: rosh
ms.openlocfilehash: c153f577f76944d22f9a1b0fb4b24d332d2a02c8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220779"
---
# <a name="video-search-endpoints"></a>Video Sök slutpunkter
Den **Bing** innehåller tre slutpunkter.  Slutpunkt 1 returnerar videor från webben baserat på en fråga. Slutpunkt 2 returnerar insikter om en video som baseras på den `modules` URL-parameter.  Slutpunkten 3 returnerar populära bilder.

## <a name="endpoints"></a>Slutpunkter
Om du vill få video resultat med hjälp av Bing-API kan skicka en `GET` begäran till någon av följande slutpunkter. Använda rubriker och URL-parametrar för att definiera ytterligare specifikationer.

**Slutpunkt 1:** returnerar videor som är relevanta för användarens sökfråga som definieras av `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Slutpunkt 2:** returnerar insikter om en video, till exempel relaterade videor. Inkludera den `modules` [frågeparameter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), vilket är en kommaavgränsad lista över insikter för att begära.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Slutpunkt 3:** returnerar videor som är populära baserat på search-begäranden som görs av andra. Videor är indelade i olika kategorier, till exempel baserat på anmärkningsvärda personer eller händelser.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [Videosökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) referens.
## <a name="response-json"></a>Svaret JSON
Svaret på en sökbegäran videor inkluderar resultat som JSON-objekt. Exempel för att tolka resultaten finns i den [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) och [källkod](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom att longitud, latitud och Sök radius.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med API för videosökning finns [Videosökningsresultat Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).