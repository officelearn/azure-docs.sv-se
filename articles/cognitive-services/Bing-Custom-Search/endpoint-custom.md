---
title: Anpassad sökning endpoint | Microsoft Docs
description: Sammanfattning av anpassad sökning API-slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 8d9851f3687a783f52a80a8dffcf2580d4710551
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352875"
---
# <a name="custom-search"></a>Anpassad sökning
Bing anpassad sökning kan du skapa en anpassad sökning upplevelser efter avsnitt som intresserar dig. Användarna ser sökresultat som är skräddarsydda för de är intresserad av i stället för att innehållet till sidan via sökresultat som har irrelevanta innehåll.

## <a name="custom-search-endpoint"></a>Anpassad sökning slutpunkt
För att få resultat med hjälp av Bing anpassad sökning API kan skicka en `GET` begäran till följande slutpunkt. Använda sidhuvuden och URL-parametrar för att definiera ytterligare specifikationer.

Slutpunkt: Returnerar sökförslag som JSON-resultat som är relevanta för den användarindata som definierats av `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Exempel som beskriver hur du ställer in anpassad sökning datakällor finns i [kursen](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Mer information om huvuden, parametrar, marknaden koder, svar objekt, fel, etc., finns det [Bing anpassad sökning API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) referens.

## <a name="custom-search-response-json"></a>Anpassad sökning svar JSON
En anpassad sökning-begäran returnerar resultat som JSON-objekt, se [svar objekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Anpassad Autosuggest
Anpassad Autosuggest API kan du skicka en partiell fråga sökterm till Bing och få tillbaka en lista över föreslagna frågor som du kan konfigurera. Med anpassad Autosuggest Lägg till förslag som returneras av API: et och ange om du vill inkludera förslag som genererats av Bing.

## <a name="custom-autosuggest-endpoint"></a>Anpassad Autosuggest slutpunkt
Skicka en GET-begäran för att begära anpassad fråga förslag:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Information om hur du definierar anpassade förslag finns [definiera anpassade sökförslag](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Sökningen anpassad avbildning
Sök-API för anpassad bild kan du skicka en fråga till Bing och hämta en lista över relevanta bilder från anpassad sökning-instans.

## <a name="custom-image-search-endpoint"></a>Anpassad avbildning Sök slutpunkt
Om du vill begära bilder från anpassad sökning-instans, skicka en GET-begäran till följande URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Information om hur du konfigurerar en anpassad sökning instans finns [konfigurera din anpassade sökinställningar](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökning åtgärder som returnerar enligt deras typ. Alla Sök slutpunkter returnerar resultat som objekt för JSON-svar.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom longitud, latitud och RADIUS-sökning.

Fullständig information om de parametrar som stöds av varje slutpunkt finns på referenssidor för varje typ av.
Exempel på enkla begäranden med anpassad sökning API finns [anpassad sökning Quick-startar](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)