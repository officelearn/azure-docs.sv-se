---
title: Vad är Azure Custom Vision?
titlesuffix: Azure Cognitive Services
description: Lär dig att använda Custom Vision Service för att skapa anpassade bildklassificerare i Azure-molnet.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 10/26/2018
ms.author: anroth
ms.openlocfilehash: 5e8e675b32bfd4c741b82b1ab341a80adbb0529d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741648"
---
# <a name="what-is-azure-custom-vision"></a>Vad är Azure Custom Vision?

Custom Vision Service är en Cognitive-tjänst som låter dig skapa, distribuera och förbättra anpassade bildklassificerare. En bildklassificerare är en AI-tjänst som sorterar avbildningar i klasser (taggar) enligt vissa egenskaper. Till skillnad från [Visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) kan du skapa dina egna klassificeringar med Custom Vision.

## <a name="what-it-does"></a>Vad läget gör

Custom Vision-tjänsten använder en maskininlärningsalgoritm för att klassificera bilder. Du, utvecklaren, måste skicka in grupper av bilder som har och saknar de aktuella klassificeringarna. Du anger rätt taggar för bilderna vid inskickandet. Algoritmen tränas med dessa data och beräknar sin egen noggrannhet genom att testa sig själv på samma data. När modellen tränas kan du testa, träna och använda den för att klassificera nya avbildningar enligt behoven i din app. Du kan också exportera själva modellen för användning offline.

### <a name="classification-and-object-detection"></a>Klassificering och objektidentifiering

Anpassade funktioner för Custom Vision kan delas in i två funktioner. **Bildklassificering** tilldelar en fördelning av klassificeringar till varje bild. **Objektidentifiering** är liknande, men returnerar även koordinaterna i bilden där de tillämpade taggarna kan hittas.

### <a name="optimization"></a>Optimering

I allmänhet är metoderna som Custom Vision Service robusta för skillnader, vilket betyder att du kan börja skapa prototyper med små mängder data. 50 bilder per tagg är vanligtvis en bra början. Det innebär dock att tjänsten inte är optimal för att identifiera vissa skillnader i bilder (exempelvis upptäcka små fel i kvalitetskontroller).

Dessutom kan du välja från flera olika typer av Custom Vision-algoritmen som är optimerade för vissa ämnesmaterial&mdash;exempelvis landmärken eller detaljhandelsobjekt. Mer information finns i dokumentet [Skapa en klassificerare](getting-started-build-a-classifier.md).

## <a name="what-it-includes"></a>Vad verktyget innehåller
Custom Vision Service är tillgängligt som en uppsättning av anpassade SDK: er eller via ett webbaserat gränssnitt på [Custom Vision-startsidan](https://customvision.ai/). Du kan skapa, testa och träna en modell via gränssnittet, eller båda.

![Custom Vision-startsida för i ett Chrome-webbläsarfönster](media/browser-home.png)

## <a name="next-steps"></a>Nästa steg

Följ guiden [Skapa en klassificerare](getting-started-build-a-classifier.md) för att komma igång med Custom Vision på webben eller slutför en [självstudie om bildklassificering](csharp-tutorial.md) för att implementera scenariot i kod.
