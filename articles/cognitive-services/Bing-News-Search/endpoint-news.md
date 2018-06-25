---
title: Bing News Sök slutpunkter | Microsoft Docs
description: Sammanfattning av nyheterna Sök API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: b8ae99698926a818bf22b0b4027af8610413aaa6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351510"
---
# <a name="news-search-endpoints"></a>Nyheter Sök slutpunkter
Den **nyheter Sök-API** returnerar nyheter artiklar, webbsidor, bilder, videor och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Enheter som innehåller översiktsinformation om en person, plats eller avsnittet. 
## <a name="endpoints"></a>Slutpunkter
För att få nyheter sökresultat med hjälp av Bing-API kan skicka en `GET` begäran till någon av följande slutpunkter. Sidhuvuden och URL-parametrar kan definiera ytterligare specifikationer.

**Slutpunkt 1:** returnerar nyhetsartiklar baserat på användarens sökfråga. Om frågan är tom returnerar de översta nyhetsartiklar anropet. Frågan `?q=""` alternativet kan också användas med den `/news` URL. Tillgänglighet, se [länder och marknader stöds](supported-countries-markets.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search 
``` 

**Slutpunkt 2:** returnerar de senaste objekt efter kategori. Mer specifikt kan du begära översta affärs-, sport- eller artiklar med `category=business`, `category=sports`, eller `category=entertainment`.  Den `category` parametern kan bara användas med den `/news` URL. Det finns vissa formella krav för att ange kategorier. referera till `category` i den [Frågeparametern](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentation. Tillgänglighet, se [länder och marknader stöds](supported-countries-markets.md#supported-markets-for-news-endpoint). 
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Slutpunkt 3:** returnerar nyheter som för närvarande trender på sociala nätverk. När den `/trendingtopics` alternativet ingår, Bing search ignorerar flera andra parametrar, exempelvis `freshness` och `?q=""`. Tillgänglighet, se [länder och marknader stöds](supported-countries-markets.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics 
``` 

Mer information om huvuden, parametrar, marknaden koder, svar objekt, fel, etc., finns det [Bing News search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) referens.
## <a name="response-json"></a>Svaret JSON
Svaret på en sökbegäran nyheter innehåller resultatet som JSON-objekt. Tolka resultatet kräver procedurer som hanterar element för varje typ. Finns det [kursen](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) och [källkod](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) exempel.

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökning åtgärder som returnerar enligt deras typ. Alla Sök slutpunkter returnerar resultat som objekt för JSON-svar.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom longitud, latitud och RADIUS-sökning.

Fullständig information om de parametrar som stöds av varje slutpunkt finns på referenssidor för varje typ av.
Exempel på grundläggande begäranden genom att använda sökfunktionen nyheter API finns [Bing News Search Quick-startar](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).