---
title: Slutpunkt för nyhetssökning i Bing
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller en sammanfattning av API-slutpunkterna för nyhetssökning. nyheter, toppnyheter och populära nyheter.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111481"
---
# <a name="bing-news-search-api-endpoints"></a>Api-slutpunkter för Bing-nyheters sökning

**Api:et för nyhetssökning** returnerar nyhetsartiklar, webbsidor, bilder, videor och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter innehåller sammanfattande information om en person, plats eller ett ämne.

## <a name="endpoints"></a>Slutpunkter

Om du vill hämta sökresultat på nyheter med `GET` API:et för nyhetssökning på Bing news skickar du en begäran till någon av följande slutpunkter. Rubrikerna och URL-parametrarna definierar ytterligare specifikationer.

### <a name="news-items-by-search-query"></a>Nyheter efter sökfråga

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Returnerar nyheter baserat på en sökfråga. Om sökfrågan är tom returnerar API:et de vanligaste nyhetsartiklarna från olika kategorier. Skicka en fråga genom url kodning din sökterm`q=""` och lägga till den till parametern. Tillgänglighet finns i [Länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Populära nyheter efter kategori

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Returnerar de översta nyheterna efter kategori. Du kan särskilt begära de bästa affärs-, `category=business` `category=sports`sport- `category=entertainment`eller underhållningsartiklarna med , eller . Parametern `category` kan bara användas `/news` med URL:en. Det finns vissa formella krav för att ange kategorier. se `category` dokumentationen för [frågeparametern.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) Skicka en fråga genom url kodning din sökterm`q=""` och lägga till den till parametern. Tillgänglighet finns i [Länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Populära nyhetsämnen 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Returnerar nyhetsämnen som för närvarande trendar på sociala nätverk. När `/trendingtopics` alternativet ingår ignorerar Bing-sökning flera andra `freshness` parametrar, till exempel och `?q=""`. Tillgänglighet finns i [Länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Nästa steg

Mer information om rubriker, parametrar, marknadskoder, svarsobjekt, fel osv., se [Bing News search API v7-referensen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)

Fullständig information om de parametrar som stöds av varje slutpunkt finns i referenssidorna för varje typ.
Exempel på grundläggande begäranden med hjälp av API:et för nyhetssökning finns i [Snabbstartar för Bing-nyhetssökning](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
