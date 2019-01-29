---
title: Slutpunkt för nyhetssökning i Bing
titlesuffix: Azure Cognitive Services
description: Sammanfattning av nyheter search API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: v-gedod
ms.openlocfilehash: a41886927fcc34d7549cbf160dd2e7aa2e9a21c4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203682"
---
# <a name="bing-news-search-api-endpoints"></a>Nyhetssökning i Bing-slutpunkter

Den **nyheter Search API** returnerar nyheter artiklar, webbsidor, bilder, videor, och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter innehåller översiktsinformation om en person, plats eller ett ämne.

## <a name="endpoints"></a>Slutpunkter

För att få nyheter sökresultat med hjälp av den nyhetssökning i Bing kan skicka en `GET` begäran till någon av följande slutpunkter. Rubriker och URL-parametrar kan definiera ytterligare specifikationer.

### <a name="news-items-by-search-query"></a>Nyhetsartiklar av sökfråga

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Returnerar nyhetsobjekt baserat på en sökfråga. Om frågan är tom, returnerar API: et de senaste artiklarna från olika kategorier. Skicka en fråga efter url kodning sökordet och lägga till den till den`q=""` parametern. Tillgänglighet, se [länder och marknader som stöds](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>De senaste objekt efter kategori

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Returnerar de senaste artiklarna efter kategori. Mer specifikt kan du begära översta företag, sport och underhållning artiklar med `category=business`, `category=sports`, eller `category=entertainment`.  Den `category` parametern kan bara användas med den `/news` URL: en. Det finns vissa formella krav för att ange kategorier. referera till `category` i den [frågeparameter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentation. Skicka en fråga efter url kodning sökordet och lägga till den till den`q=""` parametern. Tillgänglighet, se [länder och marknader som stöds](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Populära nyhetsämnen 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Returnerar nyhetsämnen som för närvarande är populära på sociala nätverk. När den `/trendingtopics` alternativet ingår, Bing search ignorerar flera andra parametrar, till exempel `freshness` och `?q=""`. Tillgänglighet, se [länder och marknader som stöds](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Nästa steg

Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [nyhetssökning i Bing API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) referens.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med API för nyhetssökning finns [Sök på Bing News Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
