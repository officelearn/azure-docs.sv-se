---
title: Bing News Sök slutpunkter | Microsoft Docs
description: Sammanfattning av nyheter search API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: ab892e947566adf025499382b213a52ed3e96e35
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433692"
---
# <a name="news-search-endpoints"></a>Nyheter Sök slutpunkter
Den **nyheter Search API** returnerar nyheter artiklar, webbsidor, bilder, videor, och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter innehåller översiktsinformation om en person, plats eller ett ämne.
## <a name="endpoints"></a>Slutpunkter
För att få nyhetssökningsresultat med hjälp av Bing-API kan skicka en `GET` begäran till någon av följande slutpunkter. Rubriker och URL-parametrar kan definiera ytterligare specifikationer.

**Slutpunkt 1:** returnerar nyhetsobjekt baserat på användarens sökfråga. Om frågan är tom i anropet returnerar de senaste artiklarna. Frågan `?q=""` alternativet kan också användas med den `/news` URL: en. Tillgänglighet, se [länder och marknader som stöds](language-support.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

**Slutpunkt 2:** returnerar de senaste artiklarna efter kategori. Mer specifikt kan du begära översta företag, sport och underhållning artiklar med `category=business`, `category=sports`, eller `category=entertainment`.  Den `category` parametern kan bara användas med den `/news` URL: en. Det finns vissa formella krav för att ange kategorier. referera till `category` i den [frågeparameter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentation. Tillgänglighet, se [länder och marknader som stöds](language-support.md#supported-markets-for-news-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Slutpunkt 3:** returnerar nyhetsämnen som för närvarande är populära på sociala nätverk. När den `/trendingtopics` alternativet ingår, Bing search ignorerar flera andra parametrar, till exempel `freshness` och `?q=""`. Tillgänglighet, se [länder och marknader som stöds](language-support.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [nyhetssökning i Bing API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) referens.
## <a name="response-json"></a>Svaret JSON
Svaret på en sökbegäran nyheter inkluderar resultat som JSON-objekt. Parsning resultatet kräver procedurer som hanterar element i varje typ av. Se den [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) och [källkod](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) exempel.

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom att longitud, latitud och Sök radius.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med API för nyhetssökning finns [Sök på Bing News Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
