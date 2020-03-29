---
title: Hämta videor från din anpassade vy - Anpassad Bing-sökning
titleSuffix: Azure Cognitive Services
description: Översikt på hög nivå om hur du använder anpassad Bing-sökning för att hämta videor från din anpassade vy av webben.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 222256036a59c7df302546bbf82648c4d524d43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68405087"
---
# <a name="get-videos-from-your-custom-view"></a>Hämta videor från din anpassade vy

Med Bing Custom Videos Search kan du berika din anpassade sökupplevelse med videor. På ett liknande sätt som med webbresultat har anpassad sökning stöd för videosökning i listan över webbplatser i dina instanser. Du kan hämta videorna med Bings API för anpassad videosökning eller via funktionen Värdbaserade användargränssnitt. Det är enkelt att använda funktionen Hosted UI och rekommenderas för att få igång sökupplevelsen på kort tid. Information om hur du konfigurerar användargränssnittet i värddat till att inkludera videor finns i [Konfigurera din värdbaserade användargränssnittsupplevelse](hosted-ui.md).

Om du vill ha mer kontroll över visningen av sökresultaten kan du använda Bings API för sök-API för anpassade videor. Eftersom det liknar att anropa API:et för videosökning i Bing, går du till [bing-videosökning](../Bing-Video-Search/search-the-web.md) i kassan efter exempel som anropar API:et. Men innan du gör det, bekanta dig med [custom videos search API referensinnehåll.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) De största skillnaderna är de frågeparametrar som stöds (du måste inkludera parametern customConfig-fråga) och slutpunkten som du skickar begäranden till.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
