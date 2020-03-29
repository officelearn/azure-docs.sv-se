---
title: Hämta bilder från din anpassade vy - Anpassad Bing-sökning
titleSuffix: Azure Cognitive Services
description: Översikt på hög nivå om hur du använder anpassad Bing-sökning för att hämta bilder från din anpassade vy av webben.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390337"
---
# <a name="get-images-from-your-custom-view"></a>Hämta bilder från din anpassade vy

Med Bing Custom Images Search kan du berika din anpassade sökupplevelse med bilder. På ett liknande sätt som med webbresultat har anpassad sökning stöd för bildsökning i listan över webbplatser i dina instanser. Du kan hämta bilderna med Bings API för anpassad avbildningsökning eller via funktionen Värdbaserade gränssnitt. Det är enkelt att använda funktionen Hosted UI och rekommenderas för att få igång sökupplevelsen på kort tid.  Information om hur du konfigurerar användargränssnittet i värddat till att inkludera avbildningar finns i [Konfigurera din värdbaserade användargränssnittsupplevelse](hosted-ui.md).

Om du vill ha mer kontroll över visningen av sökresultaten kan du använda Bings API för sök-API för anpassade bilder. Eftersom anropa api:et liknar att anropa API:et för bing-bildsökning, går [bing-bildsökning](../Bing-Image-Search/overview.md) efter exempel som anropar API:et. Men innan du gör det, bekanta dig med [custom images search API referensinnehåll.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) De största skillnaderna är de frågeparametrar som stöds (du måste inkludera parametern customConfig-fråga) och slutpunkten som du skickar begäranden till.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
