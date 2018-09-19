---
title: Sök webbslutpunkt
titleSuffix: Azure Cognitive Services
description: Sammanfattning av Web search API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 3058ca6cf0eb99486dd4c269d43b274fb367f7a9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125467"
---
# <a name="web-search-endpoint"></a>Sök webbslutpunkt
Den **API för webbsökning** Returnerar webbsidor, nyheter, bilder, videor, och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter innehåller översiktsinformation om en person, plats eller ett ämne.
## <a name="endpoint"></a>Slutpunkt
Om du vill ha med hjälp av Bing-API för webbsökning, skicka en `GET` begäran till följande slutpunkt. Rubriker och URL-parametrar kan definiera ytterligare specifikationer.

**Slutpunkten**: returnerar Webbresultat som är relevanta för användarens sökfråga som definieras av `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Slutpunkt: Mer information om rubriker, parametrar, marknaden koder, svarsobjekt, fel, osv., finns i den [i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) referens.

## <a name="response-json"></a>Svaret JSON
Svaret på en webbfråga search innehåller alla resultat som JSON-objekt. Parsning resultatet kräver procedurer som hanterar elementen i varje typ av. Se den [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) och [källkod](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) exempel.

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom att longitud, latitud och Sök radius.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med API för webbsökning finns [söka på webben Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
