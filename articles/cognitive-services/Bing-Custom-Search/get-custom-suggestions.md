---
title: 'Bing anpassad sökning: Hämta anpassade Autosuggest förslag | Microsoft Docs'
description: Beskriver hur du hämtar anpassade automatiska förslag förslag
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352860"
---
# <a name="get-custom-suggestions"></a>Hämta anpassade förslag
Innan du skickar frågor till Bing anpassad sökning, anropa anpassade Autosuggest API för att göra sökförslag och personer. Anpassad Autosuggest API returnerar en lista över föreslagna frågor baserat på en partiell frågesträng som användaren anger. Alla relevanta anpassad fråga termer som du anger visas före förslag som genererar automatiska förslag. Mer information finns i [definiera anpassade sökförslag](define-custom-suggestions.md).

## <a name="endpoint"></a>Slutpunkt
För att få förslag frågor med Bing anpassad sökning API kan skicka en `GET` begäran till följande slutpunkt.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>Svaret JSON
Svaret innehåller en lista över SearchAction-objekt som innehåller de föreslagna sökord.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Varje förslag innehåller en `displayText` och `query` fält. Den `displayText` fältet innehåller föreslagna frågan som används för att fylla i sökrutan i listrutan.

Om användaren väljer en föreslagna fråga från den nedrullningsbara listrutan, kan du använda frågeterm i den `query` när du anropar den [Bing anpassad sökning API](overview.md).

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

- [Anropa sökningen anpassad](./search-your-custom-view.md)
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)