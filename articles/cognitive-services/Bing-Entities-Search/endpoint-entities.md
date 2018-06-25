---
title: Entiteten Sök endpoint | Microsoft Docs
description: Sammanfattning av entiteten Sök API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: a2557c6000445544b3b47a05d7d356ccaa9928b4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351402"
---
# <a name="entity-search-endpoint"></a>Entiteten Sök slutpunkt
Den **entitet Sök API** inkluderar en slutpunkt som returnerar entiteter från webben baserat på en fråga.

## <a name="endpoint"></a>Slutpunkt
Att hämta entiteten resultat med hjälp av den **Bing API**, skicka ett `GET` begäran till följande slutpunkt. Använda sidhuvuden och URL-parametrar för att definiera ytterligare specifikationer.

**Slutpunkt:** returnerar entiteter som är relevanta för användarens sökfråga definieras av `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Mer information om huvuden, parametrar, marknaden koder, svar objekt, fel, etc., se den [Bing enheten Sök API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) referens.

## <a name="response-json"></a>Svaret JSON
Svaret på en sökbegäran entiteten innehåller resultatet som JSON-objekt. Exempel på resultat finns [Kom igång](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökning åtgärder som returnerar enligt deras typ. Alla Sök slutpunkter returnerar resultat som objekt för JSON-svar.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom longitud, latitud och RADIUS-sökning.

Fullständig information om de parametrar som stöds av varje slutpunkt finns på referenssidor för varje typ av.
Exempel med hjälp av sökfunktionen API för entiteten finns [Kom igång](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) och [Resizing och Beskär miniatyrbilder](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).