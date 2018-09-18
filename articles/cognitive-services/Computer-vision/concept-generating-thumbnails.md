---
title: Generera miniatyrer - visuellt
titleSuffix: Azure Cognitive Services
description: Begrepp för att generera miniatyrer för bilder med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: ff16fa4d30fb60e6b1376027a40242af23be04d1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985359"
---
# <a name="generating-thumbnails"></a>Generera miniatyrer

En miniatyr är en liten representation av en bilden. Olika enheter, till exempel telefoner, surfplattor och datorer skapa behovet av en annan användare användarupplevelsen (UX) layouter och miniatyrer. Med hjälp av smart beskärning, löser visuellt funktionen problemet.

När du har överfört en bild, visuellt genererar en högkvalitativ miniatyr och analyserar objekten i bilden att identifiera den *intresseregionen* (ROI). Den kan eventuellt Beskär bilden för att passa kraven på avkastning. Den genererade miniatyrbilden kan anges med proportionerna som skiljer sig från proportionerna på den ursprungliga bilden att tillgodose dina behov.

Algoritmen för miniatyr fungerar på följande sätt:

1. Tar bort störande element från avbildningen och identifierar det viktigaste objektet, det intressanta området.
2. Beskär bilden baserat på identifierade region of interest.
3. Ändrar proportionerna så att den passar target miniatyrbilder.

Den genererade miniatyrbilden varierar beroende på vad du anger för höjd, bredd och smart beskärning enligt följande bild.

![Miniatyrbilder](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>Skapa miniatyrbilder exempel

I följande tabell visas vanliga miniatyrer genereras av visuellt innehåll för exempel-avbildningar. Miniatyrbilderna genererades för ett angivet mål höjden och bredden på 50 bildpunkter med smart beskärning aktiverat.

| Bild | Miniatyr |
|-------|-----------|
|![Utomhus Mountain](./Images/mountain_vista.png) | ![Anpassad för utomhusbruk Mountain miniatyr](./Images/mountain_vista_thumbnail.png) |
|![Visuellt innehåll analyserar blommor](./Images/flower.png) | ![Visuellt innehåll analyserar blommor miniatyr](./Images/flower_thumbnail.png) |
|![Kvinna tak](./Images/woman_roof.png) | ![Kvinna tak miniatyr](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).