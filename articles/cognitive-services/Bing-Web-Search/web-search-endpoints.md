---
title: Sök webbslutpunkten | Microsoft Docs
description: Översikt över Web Sök API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 72fbe1a0eb4379ad032e649f7299e37a0214adfb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351537"
---
# <a name="web-search-endpoint"></a>Sök webbslutpunkten
Den **Web Sök API** Returnerar webbsidor, nyheter, bilder, videor och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Enheter som innehåller översiktsinformation om en person, plats eller avsnittet.
## <a name="endpoint"></a>Slutpunkt
För att få sökresultaten med Bing-API kan skicka en `GET` begäran till följande slutpunkt. Sidhuvuden och URL-parametrar kan definiera ytterligare specifikationer.

**Slutpunkten**: Web returnerar resultat som är relevanta för användarens söka fråga som definieras av `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Slutpunkt: Mer information om huvuden parametrar, marknadsföra koder, svar objekt, fel, etc., finns det [Bing Web API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) referens.

## <a name="response-json"></a>Svaret JSON
Svaret på en sökbegäran Web innehåller alla resultat som JSON-objekt. Tolka resultatet kräver procedurer som hanterar elementen i varje typ av. Finns det [kursen](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) och [källkod](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) exempel.

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökning åtgärder som returnerar enligt deras typ. Alla Sök slutpunkter returnerar resultat som objekt för JSON-svar.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom longitud, latitud och RADIUS-sökning.

Fullständig information om de parametrar som stöds av varje slutpunkt finns på referenssidor för varje typ av.
Exempel på enkla begäranden som använder webbsökning API finns [söka Web Quick-startar](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).