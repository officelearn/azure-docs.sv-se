---
title: Slutpunkt för nyhetssökning i Bing
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller en sammanfattning av API-slutpunkterna för nyhets sökning. Nyheter, populära nyheter och trender.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "74111481"
---
# <a name="bing-news-search-api-endpoints"></a>API för nyhetssökning i Bing slut punkter

**Nyhetssökning-API: n** returnerar nyhets artiklar, webb sidor, bilder, videor och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter innehåller sammanfattnings information om en person, plats eller ämne.

## <a name="endpoints"></a>Slutpunkter

Om du vill få nyhets Sök resultat med API för nyhetssökning i Bing skickar `GET` du en begäran till någon av följande slut punkter. Rubrik-och URL-parametrarna definierar ytterligare specifikationer.

### <a name="news-items-by-search-query"></a>Nyhets objekt efter Sök fråga

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Returnerar nyhets objekt baserat på en Sök fråga. Om Sök frågan är tom, returnerar API: n de främsta nyhets artiklarna från olika kategorier. Skicka en fråga utifrån URL: en och koda Sök termen och Lägg till den`q=""` i parametern. För tillgänglighet, se [länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Främsta nyhets objekt efter kategori

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Returnerar de översta nyhets objekten efter kategori. Du kan särskilt beställa de översta affärs-, idrotts-och underhållnings `category=business`artiklarna `category=sports`med hjälp `category=entertainment`av, eller. `category` Parametern kan bara användas med `/news` URL: en. Det finns vissa formella krav för att ange kategorier. Läs mer `category` i dokumentationen om [Frågeparametern](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) . Skicka en fråga utifrån URL: en och koda Sök termen och Lägg till den`q=""` i parametern. För tillgänglighet, se [länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Trender i nyhets ämnen 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Returnerar nyhets ämnen som för närvarande är trender i sociala nätverk. När `/trendingtopics` alternativet är inkluderat, ignorerar Bing search flera andra parametrar, till exempel `freshness` och `?q=""`. För tillgänglighet, se [länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Nästa steg

Mer information om sidhuvuden, parametrar, marknads koder, svars objekt, fel osv. finns i [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) reference.

Fullständig information om de parametrar som stöds av varje slut punkt finns i referens sidorna för varje typ.
Exempel på grundläggande förfrågningar som använder API: t för nyhets sökning finns i [nyhetssökning i Bing snabb starter](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
