---
title: API för entitetsökning i Bing slut punkten
titleSuffix: Azure Cognitive Services
description: Lär dig mer om API för entitetsökning i Bing-slutpunkten och skicka begär anden till den.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424049"
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
> [Vad är API:et för entitetssökning i Bing?](overview.md)

## <a name="see-also"></a>Se också 

Mer information om huvuden, parametrar, marknads koder, svars objekt, fel och mer finns i referens artikeln om [API för entitetsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
