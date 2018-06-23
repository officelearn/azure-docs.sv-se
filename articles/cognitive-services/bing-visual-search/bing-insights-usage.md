---
title: Exempel på Bing insikter | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar exempel på bilden insikter som visas på Bing.com.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: 102bd0e916491738d74956c209829008d779bcbf
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354129"
---
# <a name="examples-of-bing-insights-usage"></a>Exempel på användning av Bing insights

Det här avsnittet innehåller exempel på hur Bing kan visa insikter på Bing.com.

## <a name="pagesincluding-insight-example"></a>PagesIncluding insight exempel

Nedan visas hur Bing kan visa webbsidor som innehåller bilden. I exemplet visar en länk till den första webbsidan och användaren kan expandera eller komprimera listan över andra webbsidor som innehåller bilden.

![Utökade sidor inklusive](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>ShoppingSources insight exempel

Nedan visas hur Bing kan visas i källor för produkter som visas i bilden.

![Shopping källor](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>VisualSearch insight exempel

Här visas hur Bing kan visa likartade bilder (se **relaterade bilder** i exemplet).

![Visuellt liknande bilder](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recept insight exempel

Nedan visas hur Bing kan visas med recept för den visas i bilden. Exemplet tillåter att användaren vet recept är tillgängliga.

![Recept och sidor inklusive](./media/recipes-pages-including.PNG)

 Och innehåller en länk till recept när användaren utökar listan.

![Utökade recept sidor inklusive](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>RelatedSearches insight exempel

Nedan visas hur Bing kan visa relaterade sökningar av avbildningar som görs av andra. Om användaren klickar på bilden tas användaren till Bing.com/images sökresultatsidan relaterade frågan.

![Relaterade sökningar för bilder](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Entiteten insight exempel

Nedan visas hur Bing kan visa information om entiteten (person, plats eller sak) visas i bilden. Om användaren klickar på länken entiteten tas användaren till sökresultatsidan Bing.com för entiteten.

![Entitet som visas i bild](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Visa andra insikter som användaren kan utforska

Nedan visas hur Bing kan visa annan information om den avbildning som användaren kan utforska.

![Utforska andra inblick i avbildningen](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Omgivande rutor och aktiva punkter

Icke-standard taggar är bland annat rutan som identifierar intresseområde bild taggen gäller. Om markeringsrutan inte identifierar hela bilden, kan du använda rutan för att skapa en aktiv punkt på bilden. Användaren kan klicka på den aktiva punkten för att få information som rör innehållet hittades i den aktiva punkten (eller rektangel). Till exempel om bilden är en hög sätt bild, kan resultaten innehålla taggar (och omgivande rutorna) för tillbehör som visas i bild, en vad-eller, smycken, scarfs, t.ex. I följande exempel visas en hotspot rektangeln för sunglasses som visas i bilden.

![Omgivande rutan och aktiv punkt](./media/click-to-search.PNG)



## <a name="next-steps"></a>Nästa steg

För att kolla JSON bakom de här exemplen finns [standard insikter](default-insights-tag.md) och [JSON-svar](overview.md#the-response).

Om du vill komma igång snabbt med din första begäran finns i Snabbstart: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md)





