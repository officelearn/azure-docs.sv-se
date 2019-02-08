---
title: Slutpunkt för Automatiska förslag i Bing
titlesuffix: Azure Cognitive Services
description: Sammanfattning av den automatiska förslag i Bing-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 9e80e81a79f45f132baf07af7c2e7be8f98ceec4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857557"
---
# <a name="bing-autosuggest-endpoint"></a>Slutpunkt för Automatiska förslag i Bing

Den **automatiska förslag i Bing** innehåller en slutpunkt som returnerar en lista över föreslagna frågor från en partiell sökterm.

## <a name="endpoint"></a>Slutpunkt

För att få föreslagna frågor med hjälp av Bing-API kan skicka en `GET` begäran till följande slutpunkt. Använda rubriker och URL-parametrar för att definiera ytterligare specifikationer.

**Slutpunkt:** Returnerar sökförslag som JSON-resultat som är relevanta för användarens indata som definierats av `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [automatiska förslag i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) referens.

## <a name="response-json"></a>Svaret JSON

Svaret på en begäran om automatiska förslag inkluderar resultat som JSON-objekt. Exempel för att tolka resultaten finns i den [självstudien](tutorials/autosuggest.md) och [källkod](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Nästa steg

Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.
Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom att longitud, latitud och Sök radius.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med hjälp av API för automatiska förslag i finns [automatiska förslag i Snabbstarter](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).
