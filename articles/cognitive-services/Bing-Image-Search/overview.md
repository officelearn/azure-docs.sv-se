---
title: Vad är API:et för bildsökning i Bing?
titleSuffix: Azure Cognitive Services
description: Med API:et för bildsökning i Bing kan du använda Bings kognitiva bildsökningsfunktioner i programmet. Genom att skicka användarsökfrågor med API:et kan du hämta och visa relevanta och högkvalitativa avbildningar som liknar Bing-avbildningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c52098d86efe60ee524735e6158add5260a0757f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338239"
---
# <a name="what-is-the-bing-image-search-api"></a>Vad är API:et för bildsökning i Bing?

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med API:et för bildsökning i Bing kan du använda Bings bildsökningsfunktioner i programmet. Genom att skicka sökfrågor till API:et kan du hämta högkvalitativa bilder på samma sätt som på [bing.com/images](https://www.bing.com/images).

API:et för bildsökning i Bing tillhandahåller sökresultat med bara bilder, men du kan kombinera eller använda de andra tillgängliga [API:erna för Bing-sökning](../bing-web-search/bing-api-comparison.md) för att hitta många olika typer av innehåll på webben.

## <a name="bing-image-search-features"></a>API för bildsökning i Bing – funktioner

| Funktion                                                                                                                                                                                 | Beskrivning                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Föreslå söktermer i realtid](./concepts/bing-image-search-sending-queries.md) | Förbättra programupplevelsen med [automatiska förslag i Bing](../bing-autosuggest/get-suggested-search-terms.md). Föreslagna sökord visas medan användaren skriver. |
| [Filtrera och begränsa bildresultat](./concepts/bing-image-search-get-images.md)                       | Filtrera de bilder Bing returnerar genom att redigera frågeparametrarna.                                                                                                       |
| [Beskär, ändra storlek och visa miniatyrer](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Redigera och visa miniatyrer för de bilder som returneras med bildsökningen i Bing.                                                                                      |
| [Pivotera och expandera användarsökfrågor](./concepts/bing-image-search-sending-queries.md)               | Utöka sökfunktionen genom att inkludera och visa Bing-föreslagna sökord för frågor.                                                                    |
| [Hämta populära bilder](trending-images.md)                                                                     | Anpassa en sökning efter populära bilder från hela världen.                                                                                                          |

## <a name="workflow"></a>Arbetsflöde

API:et för bildsökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda tjänsten med hjälp av antingen [REST API](./quickstarts/csharp.md)eller [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Skapa ett [Cognitive Services-API-konto](../cognitive-services-apis-create-account.md) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/cognitive-services/) kostnads fritt.
2. Skicka en begäran till API: et med en giltig [Sök fråga](./concepts/bing-image-search-sending-queries.md).
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Prova först med den [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) av API:et för bildsökning i Bing.
Den här demonstrationen visar hur du snabbt kan anpassa en sökfråga och finkamma webben i jakt på bilder.

För att komma igång snabbt med din första API-begäran kan du lära dig att:

* [Skicka sökfrågor till Bing](./quickstarts/csharp.md) med hjälp av REST API eller
* [Begära och filtrera](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) bilderna som Bing returnerar med hjälp av SDK:et.

## <a name="see-also"></a>Se även

* [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) för API:er för Bing-sökning. 

* Referensavsnittet för [API:et för bildsökning i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) innehåller information om API:ets slutpunkter, rubriker, API-svar och frågeparametrar.

* I [användnings- och visningskraven för Bing](../bing-web-search/use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.

* I artikeln om att [hämta bilder från webben med API:et för bildsökning i Bing](./concepts/bing-image-search-get-images.md) beskrivs hur du söker efter och hämtar bilder från webben.

* I artikeln om att [skicka och arbeta med sökfrågor](./concepts/bing-image-search-sending-queries.md) beskrivs hur du skapar, anpassar och pivoterar sökfrågor.

* Gå till [sidan Bing-sökning API Hub](../bing-web-search/overview.md) och utforska de andra tillgängliga API: erna.