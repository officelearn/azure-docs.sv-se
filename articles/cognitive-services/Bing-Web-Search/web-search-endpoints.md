---
title: Sök webbslutpunkt
titleSuffix: Azure Cognitive Services
description: Sammanfattning av Web search API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2f7e6cd577b1eabbaabdfe87fca8ea0f036a062d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149398"
---
# <a name="web-search-endpoint"></a>Sök webbslutpunkt

Den **API för webbsökning** Returnerar webbsidor, nyheter, bilder, videor, och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter innehåller översiktsinformation om en person, plats eller ett ämne.

## <a name="endpoint"></a>Slutpunkt

Om du vill ha med hjälp av Bing-API för webbsökning, skicka en `GET` begäran till följande slutpunkt. Rubriker och URL-parametrar kan definiera ytterligare specifikationer.

**Slutpunkt**: Returnerar Webbresultat som är relevanta för användarens sökfråga som definieras av `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Slutpunkt: Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel och mer, finns det [i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) referens.

## <a name="response-json"></a>Svaret JSON

Svaret på en webbfråga search innehåller alla resultat som JSON-objekt. Parsning resultatet kräver procedurer som hanterar elementen i varje typ av. Se den [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) och [källkod](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) exempel.

## <a name="next-steps"></a>Nästa steg

Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.  Frågor som returnerar ett visst språk och plats med longitud, latitud och Sök efter RADIUS-stöd för alla slutpunkter.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med API för webbsökning finns [söka på webben Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
