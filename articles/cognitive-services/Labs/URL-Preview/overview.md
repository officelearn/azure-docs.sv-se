---
title: Vad är Project URL Preview? -Kognitiva Microsoft-tjänster | Microsoft Docs
description: Introduktion till förhandsversionen av projekt-URL.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6b486e0ab4092bef4fe829a5f166311a572a2900
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353988"
---
# <a name="what-is-project-url-preview"></a>Vad är Project URL Preview?
URL: en förhandsgranskningsslutpunkten tar frågeparameter URL och returnerar ett JSON-svar med namnet på målresursen, en kort beskrivning och en länk till en bild som ska visas i en förhandsgranskning. Svaret innehåller också den [isFamilyFriendly](url-preview-reference.md#query-parameters) flagga som anger om URL: en innehåller vuxna oäkta eller andra ogiltigt innehåll. 

Skickar en GET-begäran för att få URL: en förhandsvisning av resultat, och inkludera den *Ocp-Apim-prenumeration-nyckeln* huvud med en giltig token:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
Svaret: 
````
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

````
## <a name="scenarios"></a>Scenarier 

API: et för URL: en Preview stöder kort beskrivning av webbresurser. Utvecklare använda för att skapa omfattande preview upplevelser.  Användare kan dela eller ett bokmärke för webbsidor, nyheter, bloggar, forum och så vidare. Detta API kan också användas för innehåll måtta.    

Program använder URL Preview webbförfrågningar skickas till slutpunkten med en fråga som tilldelats URL: en för förhandsgranskning.  JSON-svar som innehåller informationen som preview: namn, beskrivning av resursen, *familyFriendly* flaggan och länkar som ger åtkomst till en representativ bild och fullständig resursen. 

## <a name="terms-of-use"></a>Användningsvillkor
Använda endast data från Project URL: en förhandsgranskning för att visa förhandsgranskning kodavsnitt och miniatyrbilder som är länkade till sina källplatser i initierats av slutanvändaren URL delning på sociala medier, chatt bot eller liknande erbjudanden. D inte att kopiera, spara eller cachelagra alla data som du får från Project URL Preview. Respektera alla begäranden om att inaktivera förhandsversioner som du kan få från webbplatsen eller innehåll ägare.

Du eller någon tredje part å dina vägnar kan inte använda, behålla, lagra, cache, dela, eller distribuera alla data från API: et för URL-Preview för testning, utveckling, utbildning, distribuera eller gör alla icke-Microsoft-tjänster eller funktion. 

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg
- [C#-Snabbstart](csharp.md)
- [Java-Snabbstart](java-quickstart.md)
- [JavaScript-Snabbstart](javascript.md)
- [Noden Snabbstart](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)
