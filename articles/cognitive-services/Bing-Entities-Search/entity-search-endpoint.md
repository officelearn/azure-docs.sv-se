---
title: Entitetssökning i Bing-slutpunkt
titlesuffix: Azure Cognitive Services
description: Läs om API för Entitetssökning i Bing-slutpunkten och skicka begäranden till den.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 3c2aa4b22c8e679f73692978d9e1f8009f11a46b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598525"
---
# <a name="bing-entity-search-api-endpoint"></a>Entitetssökning i Bing-slutpunkt


Bing-Entitetssökning har en slutpunkt som returnerar entiteter från webben baserat på en fråga. Dessa sökresultaten returneras i JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Hämta enhetsresultat från slutpunkten

Att hämta entiteten resultat med hjälp av den **API för Bing**, skicka en `GET` begäran till följande slutpunkt. Använd [rubriker](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) och [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters) att anpassa din begäran. Sök efter ansökningar kan skickas med hjälp av den `?q=` parametern.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är API:et för entitetssökning i Bing?](overview.md)

## <a name="see-also"></a>Se också 

Läs mer om huvuden parametrar, marknaden koder, svarsobjekt, fel och den [Entitetssökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) referensartikeln.
