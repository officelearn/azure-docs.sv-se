---
title: Hämta avbildningar från den anpassade vyn | Microsoft Docs
titleSuffix: Cognitive Services
description: Översikt om hur du använder Bing Custom Search för att hämta avbildningar från din anpassade vy över webben.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: ba836bbafaf67238664862ee2afce7840a573e44
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953903"
---
# <a name="get-images-from-your-custom-view"></a>Hämta avbildningar från den anpassade vyn

Bing Custom Search i avbildningar kan du förbättra upplevelsen för anpassad sökning med bilder. Liknar Webbresultat, har stöd för anpassad sökning söker efter bilder i din instans listan över webbplatser. Du kan hämta bilder med hjälp av anpassade avbildningar i Bing eller via Användargränssnittet finns funktion. Funktionen finns Användargränssnittet är enkelt att använda och rekommenderas för din sökupplevelse konfigurera och komma igång i korthet ordning.  Information om hur du konfigurerar din värdbaserade gränssnitt för att inkludera bilder finns i [konfigurera din värdbaserade användargränssnitt](hosted-ui.md).

Om du vill ha mer kontroll över visar sökresultatet kan du använda anpassade avbildningar i Bing. Eftersom anropa API liknar anropar den bildsökning i Bing, Kolla in [bildsökning i Bing](../Bing-Image-Search/overview.md) exempel anropar API: et. Men innan du gör det bekanta dig med den [anpassade avbildningar Search API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference) innehåll. De viktigaste skillnaderna är de frågeparametrar som stöds (måste du inkludera Frågeparametern customConfig) och den slutpunkt som du skickar begäranden till.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->