---
title: Slut punkt för förhandsgranska projekt URL
titlesuffix: Azure Cognitive Services
description: Översikt över slut punkten för för hands versionen av URL.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 3ef5ebd4ec88deac8c49430f36956d3711c8c535
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706942"
---
# <a name="project-url-preview-endpoint"></a>Slut punkt för förhandsgranska projekt URL

URL: en för för hands versionen innehåller en slut punkt.

## <a name="endpoint"></a>Slutpunkt
Om du vill hämta en URL för förhands granskning skickar du en begäran till följande slut punkt. Använd sidhuvudena och URL-parametrarna för andra specifikationer.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Frågeparametrar
|Namn|Värde|type|Obligatorisk|  
|----------|-----------|----------|--------------|  
|frågor och|URL som ska förhandsgranskas|Sträng |Ja|
|safeSearch|Otillåtet innehåll i vuxna, eller pirattillverkade innehåll, blockeras med felkod 400 och flaggan *isFamilyFriendly* returneras inte. <p>För innehåll som är olämpligt för barn, nedan är beteendet. Status koden returnerar 200 och *isFamilyFriendly* -flaggan har angetts till false.<ul><li>safeSearch = strikt: Rubrik, beskrivning, URL och bild kommer inte att returneras.</li><li>safeSearch = måttlig; Hämta rubrik, URL och beskrivning, men inte en beskrivande bild.</li><li>safeSearch = off; Hämta alla svars objekt/element – rubrik, URL, beskrivning och bild.</li></ul> |Sträng|Krävs inte. </br> Standardvärdet är safeSearch = Strict.| 

## <a name="response-object"></a>Svarsobjekt

Svaret innehåller HTTP-huvuden och webb Side objekt med attribut som visas i följande exempel: `name` `description`, `url` `isFamilyFriendly`,, och `primaryImageOfPage`.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](csharp.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för JavaScript](javascript.md)
- [Snabbstart för Node](node-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)
