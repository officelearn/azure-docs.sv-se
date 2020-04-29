---
title: API för entitetsökning i Bing slut punkten
titleSuffix: Azure Cognitive Services
description: API för entitetsökning i Bing har en slut punkt som returnerar entiteter från webben baserat på en fråga. Dessa Sök Resultat returneras i JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "74072657"
---
# <a name="bing-entity-search-api-endpoint"></a>API för entitetsökning i Bing slut punkt


API för entitetsökning i Bing har en slut punkt som returnerar entiteter från webben baserat på en fråga. Dessa Sök Resultat returneras i JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Hämta enhets resultat från slut punkten

Skicka en `GET` begäran till följande slut punkt för att hämta enhets resultat med **Bing-API: et**. Använd [huvuden](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) och [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) för att anpassa din Sök förfrågan. Sök begär Anden kan skickas med hjälp `?q=` av parametern.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är API för entitetsökning i Bing?](overview.md)

## <a name="see-also"></a>Se även 

Mer information om huvuden, parametrar, marknads koder, svars objekt, fel och mer finns i referens artikeln om [API för entitetsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
