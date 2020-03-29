---
title: Slutpunkt för Anpassad sökning i Bing
titleSuffix: Azure Cognitive Services
description: Skapa skräddarsydda sökupplevelser för ämnen som du bryr dig om. Användarna ser sökresultat som är anpassade till det innehåll de bryr sig om.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072792"
---
# <a name="custom-search"></a>Anpassad sökning
Med anpassad sökning i Bing kan du skapa skräddarsydda sökningar om ämnen som intresserar dig. Användarna ser sökresultat som skräddarsytts efter det innehåll som intresserar dem, i stället för att behöva bläddra bland irrelevanta sökresultat.

## <a name="custom-search-endpoint"></a>Slutpunkt för anpassad sökning
Om du vill få resultat med api:et för anpassad sökning i Bing skickar du en `GET` begäran till följande slutpunkt. Använd rubrikerna och URL-parametrarna för att definiera ytterligare specifikationer.

Slutpunkt: Returnerar sökförslag som JSON-resultat som är relevanta `?q=""`för användarens indata som definieras av .
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Exempel som beskriver hur du ställer in anpassade sökkällor finns i [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Mer information om rubriker, parametrar, marknadskoder, svarsobjekt, fel osv., se [Bing Custom Search API v7-referensen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)

## <a name="custom-search-response-json"></a>Anpassad söksvar JSON
En anpassad sökbegäran returnerar resultat som JSON-objekt, se [Svarsobjekt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Anpassad autosuggest
Med API:et för anpassade autosuggest kan du skicka en partiell söktefrågeterm till Bing och få tillbaka en lista med föreslagna frågor som du kan konfigurera. Med Anpassad autosuggest lägger du till förslag som returneras av API:et och anger eventuellt om förslag som genereras av Bing ska inkluderas.

## <a name="custom-autosuggest-endpoint"></a>Slutpunkt för anpassat automatiskt förslag
Om du vill begära anpassade frågeförslag skickar du en GET-begäran till:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Information om hur du definierar anpassade förslag finns i [Definiera anpassade sökförslag](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Anpassad bildsökning
Med API:et för anpassad bildsökning kan du skicka en sökfråga till Bing och få tillbaka en lista över relevanta bilder från din Custom Search-instans.

## <a name="custom-image-search-endpoint"></a>Slutpunkt för anpassad bildsökning
Om du vill begära bilder från din instans för anpassad sökning skickar du en GET-begäran till följande WEBBADRESS:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Information om hur du konfigurerar en anpassad sökinstans finns i [Konfigurera din anpassade sökupplevelse](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Nästa steg
Bing-API:erna stöder sökåtgärder som returnerar resultat efter typ. **Bing**Alla sökslutpunkter returnerar resultat som JSON-svarsobjekt. Alla slutpunkter stöder frågor som returnerar ett visst språk och/eller en viss plats med longitud, latitud och sökradie.

Fullständig information om de parametrar som stöds av varje slutpunkt finns i referenssidorna för varje typ.
Exempel på grundläggande begäranden med hjälp av API:et för anpassad sökning finns i [Snabbstartar för anpassad sökning](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
