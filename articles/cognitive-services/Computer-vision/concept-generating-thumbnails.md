---
title: Generera miniatyrer - visuellt
titleSuffix: Azure Cognitive Services
description: Begrepp för att generera miniatyrer för bilder med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pafarley
ms.openlocfilehash: 7d914f394ecfcf02ed26f41cd8fe2ef799cf6103
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966746"
---
# <a name="generating-thumbnails"></a>Generera miniatyrer

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
|![Berg utomhus](./Images/mountain_vista.png) | ![Anpassad för utomhusbruk Mountain miniatyr](./Images/mountain_vista_thumbnail.png) |
|![Visuellt innehåll analyserar blommor](./Images/flower.png) | ![Visuellt innehåll analyserar blommor miniatyr](./Images/flower_thumbnail.png) |
|![Kvinna tak](./Images/woman_roof.png) | ![Kvinna tak miniatyr](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).