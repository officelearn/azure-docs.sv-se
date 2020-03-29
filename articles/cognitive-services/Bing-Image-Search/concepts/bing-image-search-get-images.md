---
title: Hämta bilder från webben - API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Använd API:et för bing-bildsökning för att söka efter och hämta relevanta bilder från webben.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 309bbca762149f8804742d9ef02d4c3e8dfcdc6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542770"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Hämta bilder från webben med API:et för bildsökning i Bing

När du använder REST-API:ET för Bing Image Search kan du hämta bilder från webben som är relaterade till din sökterm genom att skicka följande GET-begäran:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Använd [parametern q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) query för din url-kodade sökterm. Om du till exempel anger *seglingsjollar*anger du `q` till `sailing+dinghies` eller `sailing%20dinghies`.

> [!IMPORTANT]
> * Alla begäranden måste göras från en server och inte från en klient.
> * Om det är första gången du anropar någon av Bing-sök-API:erna ska du inte inkludera klient-ID-huvudet. Inkludera endast klient-ID:t om du tidigare har anropat ett Bing-API som returnerade ett klient-ID för användar- och enhetskombinationen.

## <a name="get-images-from-a-specific-web-domain"></a>Hämta bilder från en viss webbdomän

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Svar på frågor `site:` som använder operatören kan innehålla barnförbjudet innehåll oavsett [safeSearch-inställningen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) Använd `site:` endast om du är medveten om innehållet på domänen.

## <a name="filter-images"></a>Filtrera bilder

 Som standard returnerar API:et för bildsökning alla bilder som är relevanta för frågan. Om du vill filtrera de bilder som Bing returnerar (till exempel om du bara vill returnera bilder med genomskinlig bakgrund eller specifik storlek) använder du följande frågeparametrar:

* [aspekt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)– Filtrera bilder efter bildförhållande (till exempel standard- eller widescreen-bilder).
* [färg](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)– Filtrera bilder efter dominerande färg eller svartvitt.
* [fräschör](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)– Filtrera bilder efter ålder (till exempel bilder som upptäckts av Bing under den senaste veckan).
* [höjd](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [bredd](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)– Filtrera bilder efter bredd och höjd.
* [bildInnehåll –](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)Filtrera bilder efter innehåll (till exempel bilder som bara visar en persons ansikte).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)– Filtrera bilder efter typ (till exempel ClipArt, animerade GIF-bilder eller genomskinliga bakgrunder).
* [licens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)– Filtrera avbildningar efter den typ av licens som är associerad med webbplatsen.
* [storlek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)– Filtrera bilder efter storlek, till exempel små bilder upp till 200 x 200 pixlar.

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](https://msdn.microsoft.com/library/ff795613.aspx).

Följande exempel visar hur du får små bilder från ContosoSailing.com som Bing upptäckte under den senaste veckan.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Svarsformat för bing-bildsökning

Svarsmeddelandet från Bing innehåller ett [bildsvar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) som innehåller en lista med bilder som Cognitive Services har fastställt som relevanta för frågan. Varje [bildobjekt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) i listan innehåller följande information om bilden: URL: dess storlek, dess dimensioner, dess kodningsformat, en URL till en miniatyrbild av bilden och miniatyrens dimensioner.

> [!NOTE]
> * Bilder måste visas i den ordning som anges i svaret.
> * Eftersom URL-format och parametrar kan komma att ändras utan föregående meddelande använder du alla webbadresser som de är. Du bör inte ta beroenden på URL-format eller parametrar utom när det anges.

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

När du anropar API för bildsökning i Bing returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. Svarets fält `totalEstimatedMatches` innehåller en uppskattning av det antal bilder som är tillgängliga för visning. Mer information om hur du bläddrar igenom resten av bilderna finns i [Växlingsbilder](../paging-images.md).

## <a name="next-steps"></a>Nästa steg

Om du inte har provat API:et för bing-bildsökning tidigare provar du en [snabbstart](../quickstarts/csharp.md). Om du letar efter något mer komplext kan du prova självstudien för att skapa en [webbsida](../tutorial-bing-image-search-single-page-app.md).
