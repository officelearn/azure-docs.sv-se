---
title: Entiteten Sök slutpunkter | Microsoft Docs
description: Sammanfattning av entiteten Sök API-slutpunkt.
services: cognitive-services
author: v-jaswel
manager: kaiq
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: 3d5da30102712baf399c245cc7678eeddbce796a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351420"
---
# <a name="entity-search-endpoints"></a>Entiteten Sök slutpunkter
Den **entitet Sök API** inkluderar en slutpunkt.

## <a name="endpoint"></a>Slutpunkt
Om du vill begära entitet sökresultat skicka en begäran till följande slutpunkt. Använda sidhuvuden och URL-parametrar för att definiera ytterligare specifikationer.

Slutpunkt `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Följande URL-parametrar är obligatoriska:
- mkt. Marknaden var resultatet ska hämtas. 
- frågor. Entiteten sökfråga.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart för Bing enheten sökning](quickstarts/csharp.md)

## <a name="see-also"></a>Se också 

[Översikt över Bing enheten Sök](search-the-web.md )
[API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
