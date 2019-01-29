---
title: Hämta bilder från webben - bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Använda sökning i Bing för att söka efter och hämta relevanta bilder från webben.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: e91270280633e25c71758d73b94fea4db19db17f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152324"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Hämta avbildningar från webben med sökning i Bing

När du använder Search REST API för Bing-avbildning kan hämta du bilder från webben som är relaterade till sökordet genom att skicka följande GET-begäran:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * Alla begäranden måste göras från en server och inte från en klient.
> * Om det är första gången du anropar någon av Bing search API: er omfattar inte rubrik för klient-ID. Inkludera endast klient-ID om du har tidigare kallade ett Bing-API som returnerade ett klient-ID för användaren och enheten kombination.
> * Avbildningar måste visas i den ordning som anges i svaret.

## <a name="get-images-from-a-specific-web-domain"></a>Hämta bilder från en specifik domän

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Svar på frågor med hjälp av den `site:` operatör kan innehålla vuxet innehåll oavsett den [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) inställningen. Använd bara `site:` om du är medveten om innehållet på domänen.

I följande exempel visas hur du hämtar små bilder från ContosoSailing.com som Bing har identifierat den senaste veckan.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filter-images"></a>Filtrera avbildningar

 Image Search API returnerar som standard alla avbildningar som är relevanta för frågan. Om du vill filtrera de avbildningar som Bing returnerar (t.ex, för att returnera endast bilder med en genomskinlig bakgrund eller en viss storlek) använder du följande frågeparametrar:

* [aspekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect)– filtrera avbildningar efter proportionerna (till exempel standard- eller wide skärmbilder).
* [färg](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color)– filtrera avbildningar från dominant färg eller svart och vit.
* [färskhet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness)– filtrera avbildningar efter ålder (till exempel bilder som identifierats av Bing under den senaste veckan).
* [höjd](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [bredd](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width)– filtrera avbildningar från bredd och höjd.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent)– filtrera avbildningar efter innehåll (till exempel bilder som visar endast en persons ansikte).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype)– filtrera avbildningar efter typ (till exempel ClipArt, animerade GIF-filer eller transparent bakgrund).
* [licens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license)– filtrera avbildningar av typ av licens kopplad till platsen.
* [storlek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size)– filtrera avbildningar efter storlek, till exempel små avbildningar upp till 200 x 200 bildpunkter.

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](https://msdn.microsoft.com/library/ff795613.aspx).

 > [!NOTE]
 > Svar på frågor med hjälp av den `site:` operatör kan innehålla vuxet innehåll oavsett den [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) inställningen. Använd bara `site:` om du är medveten om innehållet på domänen.

I följande exempel visar hur du hämtar små bilder från ContosoSailing.com som Bing identifierats under den senaste veckan.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bildsökning i Bing svarsformat

Svarsmeddelandet från Bing innehåller en [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) svar som innehåller en lista med avbildningar som Cognitive Services är relevant för frågan. Varje [bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objekt i listan innehåller följande information om avbildningen: URL: en, dess storlek, dess dimensioner, dess kodningsformat, en URL till en miniatyrbild för avbildningen och den miniatyrbilden dimensioner.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

När du anropar API för bildsökning i Bing returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. Svarets fält `totalEstimatedMatches` innehåller en uppskattning av det antal bilder som är tillgängliga för visning. Mer information om hur du bläddrar igenom resten av avbildningarna finns i [växling avbildningar](../paging-images.md).

## <a name="next-steps"></a>Nästa steg

Om du inte har testat den bildsökning i Bing innan du prova en [snabbstarten](../quickstarts/csharp.md). Om du letar efter ett mer komplext prova guiden att skapa en [enkelsidiga webbapp](../tutorial-bing-image-search-single-page-app.md).
