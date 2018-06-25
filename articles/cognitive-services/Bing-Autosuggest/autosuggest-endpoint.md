---
title: Autosuggest endpoint | Microsoft Docs
description: Sammanfattning av Autosuggest API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 5aaddd09006cb6f1812bb6ae213a2f5e6720fb1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354492"
---
# <a name="autosuggest-endpoint"></a>Autosuggest slutpunkt

Den **Autosuggest API** inkluderar en slutpunkt som returnerar en lista över föreslagna frågor från en partiell sökterm.

## <a name="endpoint"></a>Slutpunkt

För att få förslag frågor med Bing-API kan skicka en `GET` begäran till följande slutpunkt. Använda sidhuvuden och URL-parametrar för att definiera ytterligare specifikationer.

**Slutpunkt:** returnerar sökförslag som JSON-resultat som är relevanta för den användarindata som definierats av `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Mer information om huvuden, parametrar, marknaden koder, svar objekt, fel, etc., finns det [Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) referens.

## <a name="response-json"></a>Svaret JSON

Svaret på en begäran om automatiska förslag innehåller resultatet som JSON-objekt. Exempel för att tolka resultaten finns i [kursen](tutorials/autosuggest.md) och [källkod](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Nästa steg

Den **Bing** API: er stöder sökning åtgärder som returnerar enligt deras typ. Alla Sök slutpunkter returnerar resultat som objekt för JSON-svar.
Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom longitud, latitud och RADIUS-sökning.

Fullständig information om de parametrar som stöds av varje slutpunkt finns på referenssidor för varje typ av.
Exempel på enkla begäranden med Autosuggest-API finns [Autosuggest Snabbstart](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).