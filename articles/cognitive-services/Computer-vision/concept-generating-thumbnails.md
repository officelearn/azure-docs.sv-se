---
title: Smartbeskurna miniatyrer - Datorseende
titleSuffix: Azure Cognitive Services
description: Begrepp relaterade till att generera miniatyrer för bilder med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68945221"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generera smartbeskurna miniatyrer med datorseende

En miniatyrbild är en representation av en bild med reducerad storlek. Miniatyrer används för att representera bilder och andra data på ett mer ekonomiskt, layoutvänligt sätt. API:et för visuellt innehåll använder smart beskärning, tillsammans med storleksändring av bilden, för att skapa intuitiva miniatyrer för en viss bild.

Miniatyrgenereringsalgoritmen för visuellt innehåll fungerar på följande sätt:

1. Ta bort distraherande element från bilden och identifiera _det område av intresse_&mdash;området i bilden där huvudobjektet(erna) visas.
1. Beskär bilden baserat på det identifierade _området av intresse_.
1. Ändra proportionerna så att det passar målminiatyrdimensionerna.

## <a name="area-of-interest"></a>Intresseområde

När du laddar upp en bild analyserar API:et för visuellt innehåll den för att fastställa *det område av intresse*. Den kan sedan använda det här området för att bestämma hur bilden ska beskäras. Beskärningsåtgärden matchar dock alltid önskat proportioner om en sådan anges.

Du kan också hämta de råa begränsningsramkoordinaterna för samma *intresseområde* genom att anropa **areaOfInterest** API istället. Du kan sedan använda den här informationen för att ändra den ursprungliga bilden hur du vill.

## <a name="examples"></a>Exempel

Den genererade miniatyrbilden kan variera kraftigt beroende på vad du anger för höjd, bredd och smart beskärning, som visas i följande bild.

![En bergsbild bredvid olika beskärningskonfigurationer](./Images/thumbnail-demo.png)

Följande tabell illustrerar typiska miniatyrer som genereras av Datorseende för exempelbilderna. Miniatyrerna genererades för en angiven målhöjd och bredd på 50 pixlar, med smart beskärning aktiverad.

| Bild | Miniatyr |
|-------|-----------|
|![Utomhusberg vid solnedgången, med en persons silhuett](./Images/mountain_vista.png) | ![Miniatyr av Outdoor Mountain vid solnedgången, med en persons silhuett](./Images/mountain_vista_thumbnail.png) |
|![En vit blomma med grön bakgrund](./Images/flower.png) | ![Vision Analysera Flower miniatyr](./Images/flower_thumbnail.png) |
|![En kvinna på taket till ett flerfamiljshus](./Images/woman_roof.png) | ![miniatyr av en kvinna på taket av ett hyreshus](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Nästa steg

Läs mer om [att tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).
