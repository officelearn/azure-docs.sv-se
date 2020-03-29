---
title: Slutpunkt för webbsökning
titleSuffix: Azure Cognitive Services
description: Om du vill hämta `GET` sökresultat på webben skickar du en begäran till följande slutpunkt. Rubrikerna och URL-parametrarna definierar ytterligare specifikationer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111331"
---
# <a name="web-search-endpoint"></a>Slutpunkt för webbsökning

**Api:et för webbsökning** returnerar webbsidor, nyheter, bilder, videor och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter har sammanfattande information om en person, plats eller ett ämne.

## <a name="endpoint"></a>Slutpunkt

Om du vill hämta webbsökresultat med `GET` hjälp av Bing API skickar du en begäran till följande slutpunkt. Rubrikerna och URL-parametrarna definierar ytterligare specifikationer.

**Slutpunkt**: Returnerar webbresultat som är relevanta för `?q=""`användarens sökfråga som definieras av .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Slutpunkt: Mer information om rubriker, parametrar, marknadskoder, svarsobjekt, fel med mera finns i [Bing Web API v7-referensen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

## <a name="response-json"></a>Svar JSON

Svaret på en webbsökningsbegäran innehåller alla resultat som JSON-objekt. Tolkning av resultatet kräver procedurer som hanterar elementen för varje typ. Mer om självstudie och källkod finns i [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) och [källkoden.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search)

## <a name="next-steps"></a>Nästa steg

Bing-API:erna stöder sökåtgärder som returnerar resultat efter typ. **Bing**Alla sökslutpunkter returnerar resultat som JSON-svarsobjekt. Alla slutpunkter stöder frågor som returnerar ett visst språk och en viss plats efter longitud, latitud och sökradie.

Fullständig information om de parametrar som stöds av varje slutpunkt finns i referenssidorna för varje typ.
Exempel på grundläggande begäranden med webbsök-API finns [i Söka efter snabbstarter på webben](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
