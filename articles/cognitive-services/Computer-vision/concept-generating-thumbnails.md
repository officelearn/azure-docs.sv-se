---
title: Generera miniatyrer - visuellt
titleSuffix: Azure Cognitive Services
description: Begrepp för att generera miniatyrer för bilder med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: cea8522a9f3eb8fa98821c1cb08d92a9524d5ce4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876807"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generera beskärs av smarta miniatyrbilder med visuellt innehåll

En miniatyr är en mindre representation av en avbildning. Miniatyrer används för att representera bilder och andra data på ett mer prisvärt och layout eget sätt. API för visuellt innehåll används smart beskärning, tillsammans med ändrar storlek på bilden, för att skapa intuitiva miniatyrer för en viss avbildning.

Algoritmen för visuellt skapa miniatyrbilder fungerar på följande sätt:

1. Ta bort störande element från avbildningen och identifiera de _intresseområde_&mdash;delar av bilden där de viktigaste objekten visas.
1. Beskär bilden baserat på den identifierade _intresseområde_.
1. Ändra höjd-/ breddförhållandet så att den passar target miniatyrbilder.

## <a name="area-of-interest"></a>Intresseområde

När du laddar upp en bild i API för visuellt innehåll analyserar den för att fastställa den *intresseområde*. Den här regionen kan sedan användas för att avgöra hur du Beskär bilden. Åtgärden beskärning dock matchar alltid de önskade proportionerna anges.

Du kan också hämta rådata omgivande box koordinaterna för det här samma *intresseområde* genom att anropa den **areaOfInterest** API: et istället. Du kan sedan använda den här informationen för att ändra originalbilden men du vill.

## <a name="examples"></a>Exempel

Den genererade miniatyrbilden varierar beroende på vad du anger för höjd, bredd och smart beskärning enligt följande bild.

![Miniatyrbilder](./Images/thumbnail-demo.png)

I följande tabell visas vanliga miniatyrer genereras av visuellt innehåll för exempel-avbildningar. Miniatyrbilderna genererades för ett angivet mål höjden och bredden på 50 bildpunkter med smart beskärning aktiverat.

| Bild | Miniatyr |
|-------|-----------|
|![En person som står på en mountain rock på solnedgång](./Images/mountain_vista.png) | ![Anpassad för utomhusbruk Mountain miniatyr](./Images/mountain_vista_thumbnail.png) |
|![En vit blommor med en grön bakgrund](./Images/flower.png) | ![Visuellt innehåll analyserar blommor miniatyr](./Images/flower_thumbnail.png) |
|![En kvinna under taket i en innesluten byggnad](./Images/woman_roof.png) | ![Kvinna tak miniatyr](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).
