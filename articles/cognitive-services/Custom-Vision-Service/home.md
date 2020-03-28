---
title: Vad är Custom Vision?
titleSuffix: Azure Cognitive Services
description: Lär dig att använda Custom Vision Service för att skapa anpassade bildklassificerare i Azure-molnet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a48c553f1c96b8777e0a591f428dca3f15d7d30e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053642"
---
# <a name="what-is-custom-vision"></a>Vad är Custom Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Custom Vision är en kognitiv tjänst som du kan använda för att skapa, distribuera och förbättra dina egna bildklassificerare. En bildklassificerare är en AI-tjänst som applicerar etiketter (som representerar _klasser_) på bilder utefter deras visuella egenskaper. Till skillnad från tjänsten [Visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) gör Custom Vision att du kan bestämma vilka etiketter som ska appliceras.

## <a name="what-it-does"></a>Vad läget gör

Custom Vision-tjänsten använder en maskininlärningsalgoritm för att applicera etiketter på bilder. Du, utvecklaren, måste skicka in grupper av bilder som har respektive saknar de aktuella egenskaperna. Du märker själv bilderna när de skickas in. Sedan tränas algoritmen med dessa data och beräknar sin egen noggrannhet genom att testa sig på samma bilder. När algoritmen har tränats kan du testa, träna om och senare använda den för att klassificera nya avbildningar enligt appens behov. Du kan också exportera själva modellen för användning offline.

### <a name="classification-and-object-detection"></a>Klassificering och objektidentifiering

Anpassade funktioner för Custom Vision kan delas in i två funktioner. **Bildklassificering** applicerar en eller flera etiketter på en bild. **Objektidentifiering** är liknande, men returnerar även de koordinater i bilden där de tillämpade etiketterna kan hittas.

### <a name="optimization"></a>Optimering

Tjänsten Custom Vision är optimerad för att snabbt känna igen stora skillnader mellan bilder, så att du kan börja prototypera din modell med en liten mängd data. 50 bilder per etikett är vanligtvis en bra utgångspunkt. Tjänsten är dock inte optimal för att upptäcka subtila skillnader i bilder (till exempel upptäcka mindre sprickor eller bucklor i kvalitetssäkringsscenarier).

Dessutom kan du välja bland flera olika typer av Custom Vision-algoritmen som är optimerade för bilder med vissa motiv&mdash; exempelvis landmärken eller detaljhandelsobjekt. Mer information finns i guiden [Skapa en klassificerare.](getting-started-build-a-classifier.md)

## <a name="what-it-includes"></a>Vad verktyget innehåller

Custom Vision Service är tillgängligt som en uppsättning av anpassade SDK: er eller via ett webbaserat gränssnitt på [Custom Vision-startsidan](https://customvision.ai/). Du kan skapa, testa och träna en modell via endera gränssnittet eller använda båda tillsammans.

![Custom Vision-startsida för i ett Chrome-webbläsarfönster](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder Custom Vision-tjänsten känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Följ guiden [Skapa en klassificerare](getting-started-build-a-classifier.md) för att komma igång med Custom Vision på webben eller slutför en [självstudie om bildklassificering](csharp-tutorial.md) för att implementera ett grundläggande scenario i kod.
