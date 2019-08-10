---
title: Slut punkt för Webbs ökning
titleSuffix: Azure Cognitive Services
description: Sammanfattning av API-slutpunkten för Webbs ökning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: be622c5636c253c48bec4d67fba58319262c2603
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883567"
---
# <a name="web-search-endpoint"></a>Webbsökning slut punkt

**Webbsökning-API: et** returnerar webb sidor, nyheter, bilder, videor och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter har sammanfattnings information om en person, plats eller ämne.

## <a name="endpoint"></a>Slutpunkt

Skicka en `GET` begäran till följande slut punkt om du vill få webb Sök resultat med Bing-API: et. Rubrik-och URL-parametrarna definierar ytterligare specifikationer.

**Slutpunkt**: Returnerar webb resultat som är relevanta för användarens Sök fråga som definieras av `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Endpoint Mer information om sidhuvuden, parametrar, marknads koder, svars objekt, fel och mer finns i [Bing Web API v7-](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) referensen.

## <a name="response-json"></a>Svars-JSON

Svaret på en Webbs öknings förfrågan innehåller alla resultat som JSON-objekt. Parsning av resultatet kräver procedurer som hanterar elementen i varje typ. Se självstudien och [käll koden](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) för exempel. [](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app)

## <a name="next-steps"></a>Nästa steg

API: erna för **Bing** stöder Sök åtgärder som returnerar resultat enligt deras typ. Alla Sök slut punkter returnerar resultat som JSON-svars objekt.  Alla slut punkter har stöd för frågor som returnerar ett särskilt språk och en specifik plats efter longitud, latitud och Sök-radie.

Fullständig information om de parametrar som stöds av varje slut punkt finns i referens sidorna för varje typ.
Exempel på grundläggande förfrågningar som använder API: et för Webbs ökning finns i [söka på webben snabb starter](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
