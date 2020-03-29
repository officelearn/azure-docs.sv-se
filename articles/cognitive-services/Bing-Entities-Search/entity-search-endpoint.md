---
title: Slutpunkten för API-slutpunkt för Bing-entitetssökning
titleSuffix: Azure Cognitive Services
description: Api:et för sökning av Bing-enhet har en slutpunkt som returnerar entiteter från webben baserat på en fråga. Sökresultaten returneras i JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072657"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entitetssökning API-slutpunkt


Api:et för sökning av Bing-enhet har en slutpunkt som returnerar entiteter från webben baserat på en fråga. Sökresultaten returneras i JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Hämta entitetsresultat från slutpunkten

Om du vill hämta entitetsresultat med hjälp av **Bing API**skickar du en `GET` begäran till följande slutpunkt. Använd [rubriker](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) och [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) för att anpassa sökbegäran. Sökbegäranden kan skickas `?q=` med parametern.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är API:et för sökning av Bing-entitet?](overview.md)

## <a name="see-also"></a>Se även 

Mer information om rubriker, parametrar, marknadskoder, svarsobjekt, fel med mera finns i referensartikeln [för Bing Entitetssökning v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
