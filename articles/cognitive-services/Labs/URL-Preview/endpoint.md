---
title: Projektet URL förhandsgranskningsslutpunkten - kognitiva Microsoft-tjänster | Microsoft Docs
description: 'Sammanfattning av URL: en förhandsgranskningsslutpunkten.'
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: ddd53aa49db01d7a6db397eb285d0854edc59388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353997"
---
# <a name="project-url-preview-endpoint"></a>Project URL förhandsgranskningsslutpunkten

API för Certifikatwebbadress Preview inkluderar en slutpunkt.

## <a name="endpoint"></a>Slutpunkt
Skicka en begäran till följande slutpunkt för att få en URL-förhandsgranskning. Använd rubriker och URL-parametrarna för andra specifikationer.

GET:
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

````

### <a name="query-parameters"></a>Frågeparametrar
|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|Q|URL för förhandsgranskning|Sträng |Ja|
|säker sökning|Ogiltigt innehåll för vuxna eller oäkta innehåll blockeras med felkoden 400, och *isFamilyFriendly* flaggan returneras inte. <p>För juridiska vuxet innehåll, görs nedan. Statuskoden returnerar 200, och *isFamilyFriendly* -flaggan inställd på false.<ul><li>säker sökning = strict: rubrik, beskrivning, URL och image kommer inte att returneras.</li><li>säker sökning = måttlig; Hämta rubrik, URL och beskrivning, men inte beskrivande avbildningen.</li><li>säker sökning = off; Hämta alla svar objekt/element – namn, URL, beskrivning, bild.</li></ul> |Sträng|Krävs inte. </br> Standardinställningen är säker sökning = strikt.| 

## <a name="response-object"></a>Objektet Response

Svaret innehåller HTTP-huvuden och webbsidan objekt har attribut som visas i följande exempel: `name`, `url`, `description`, `isFamilyFriendly`, och `primaryImageOfPage`.

````
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

````

## <a name="next-steps"></a>Nästa steg
- [C#-Snabbstart](csharp.md)
- [Java-Snabbstart](java-quickstart.md)
- [JavaScript-Snabbstart](javascript.md)
- [Noden Snabbstart](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)
