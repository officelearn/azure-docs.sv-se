---
title: Exempel på Bing-insikter - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller exempel på hur Bing Visual Search kan använda och visa bildinsikter på Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111631"
---
# <a name="examples-of-bing-insights-usage"></a>Exempel på användning av Bing-insikter

Den här artikeln innehåller exempel på hur Bing kan använda och visa bildinsikter på Bing.com.

## <a name="pagesincluding-insight-example"></a>SidorInkludera insiktsexempel

I följande visas en länk till den första webbsidan och användaren kan expandera och komprimera listan över andra webbsidor som innehåller bilden:

![Utökade sidor inklusive](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Exempel på shoppingsources-statistik

Följande visar hur Bing kan visa shoppingkällor för produkter som visas i bilden:

![Shoppingkällor](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Exempel på VisualSearch-insikt

Följande visar hur Bing kan visa visuellt liknande bilder (se **Relaterade bilder** i exemplet):

![Visuellt likartade bilder](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exempel på receptinsikt

Följande visar hur Bing kan visa recept för maten som visas i bilden. Exemplet låter användaren veta att det finns recept tillgängliga:

![Recept och sidor inklusive](./media/recipes-pages-including.PNG)

 Och ger länken till recepten när användaren utökar listan:

![Utökade receptsidor inklusive](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches insiktsexempel

Följande visar hur Bing kan visa relaterade sökningar av bilder gjorda av andra. Om användaren klickar på bilden tas användaren till Bing.com/images sökresultatsida för den relaterade frågan.

![Relaterade sökningar efter bilder](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Exempel på entitetsinsikt

Följande visar hur Bing kan visa information om entiteten (person, plats eller sak) som visas i bilden. Om användaren klickar på entitetslänken tas användaren till Bing.com sökresultatsidan för entiteten:

![Entitet som visas i bild](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Visa andra insikter som användaren kan utforska

Följande visar hur Bing kan visa annan information om bilden som användaren kan utforska.

![Utforska andra insikter om bilden](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Begränsningsrutor och aktiva punkter

Icke-standardtaggar inkluderar markeringsramen som identifierar det intresseområde som är av intresse för bilden som taggen gäller för. Om markeringsramen inte identifierar hela bilden använder du markeringsramen för att skapa en aktiv punkt i bilden. Användaren kan klicka på den aktiva punkten för att få information om innehållet som finns under aktiv punkt (eller rektangel). Om bilden till exempel är en modebild kan resultatet innehålla taggar (och markeringsrutor) för tillbehör som visas i bilden, till exempel en handväska, smycken, halsdukar och så vidare. I följande exempel visas en rektangel med aktiva punkter för solglasögonen som visas i bilden:

![Begränsningsram och aktiv punkt](./media/click-to-search.PNG)

## <a name="next-steps"></a>Nästa steg

För att komma igång med din första begäran, se snabbstarter: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





