---
title: Slutpunkt för Anpassad sökning i Bing
titlesuffix: Azure Cognitive Services
description: Sammanfattning av Bing Custom Search API-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: v-gedod
ms.openlocfilehash: d01f863f0c42754e2dcc36f6cab1bed1f3dc3b6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60592262"
---
# <a name="custom-search"></a>Anpassad sökning
Med anpassad sökning i Bing kan du skapa skräddarsydda sökningar om ämnen som intresserar dig. Användarna ser sökresultat som skräddarsytts efter det innehåll som intresserar dem, i stället för att behöva bläddra bland irrelevanta sökresultat.

## <a name="custom-search-endpoint"></a>Anpassad sökning
För att få resultat med hjälp av API: et för Bing Custom Search kan skicka en `GET` begäran till följande slutpunkt. Använda rubriker och URL-parametrar för att definiera ytterligare specifikationer.

Slutpunkt: Returnerar sökförslag som JSON-resultat som är relevanta för användarens indata som definierats av `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Exempel som beskriver hur du ställer in anpassad sökning i källor, finns det [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [anpassad sökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) referens.

## <a name="custom-search-response-json"></a>Anpassad sökning i svaret JSON
En anpassad sökning i begäran returnerar resultat som JSON-objekt, se [svarsobjekten](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Anpassade automatiska förslag
Anpassade automatiska förslag i API: et kan du skicka en fråga för partiella sökterm till Bing och få tillbaka en lista över föreslagna frågor som du kan konfigurera. Med anpassade automatiska förslag att lägga till förslag som returneras av API: et och du kan också ange om du vill inkludera förslag som genererats av Bing.

## <a name="custom-autosuggest-endpoint"></a>Anpassade automatiska förslag slutpunkt
Skicka en GET-begäran för att begära anpassade frågeförslag:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Information om hur du definierar anpassade förslag finns i [definiera anpassade sökförslag](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Anpassade bildsökning
Sök-API för anpassad bild kan du skicka en sökfråga till Bing och hämta en lista över relevanta avbildningar från din anpassade Sökinstans.

## <a name="custom-image-search-endpoint"></a>Anpassad avbildning Search slutpunkt
Om du vill begära avbildningar från din anpassade Sökinstans, skicka en GET-begäran till följande URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Information om hur du konfigurerar en anpassad Sökinstans finns i [konfigurera din anpassade sökupplevelse](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom att longitud, latitud och Sök radius.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med hjälp av API för anpassad sökning finns [anpassad sökning i Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
