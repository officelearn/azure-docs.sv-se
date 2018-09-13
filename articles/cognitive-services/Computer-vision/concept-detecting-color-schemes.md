---
title: Identifiera färgscheman
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Begrepp för identifiering av färgschemat i bilder med hjälp av visuellt innehåll i Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 66abab93ba9c1152d18428e66d648c6ba690aaa0
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725454"
---
# <a name="detecting-color-schemes"></a>Identifiera färgscheman

Visuellt innehåll extraherar färger från en avbildning. Färgerna analyseras sedan i tre olika kontexter: dominerande förgrundsfärgen dominerande bakgrundsfärgen och dominanta färger för avbildningen som helhet. De grupperas i 12 dominanta accentfärger. Dessa accentfärger är svarta, blå, brown, grå, grön, orange, rosa, lila, röd, mörkturkos, vit och gul. Visuellt innehåll analyserar de färger som extraheras från en avbildning för att returnera en accentfärg som representerar den mest levande färgen för bilden till användarna genom en kombination av dominanta färger och mättnad. Beroende på färger i en bild returneras enkel svartvit eller accentfärger i hexadecimal färgkoder. Visuellt också returnerar ett booleskt värde som anger om en bild är svart och vit.

## <a name="color-scheme-detection-examples"></a>Färgschema identifiering exempel

I följande exempel visas JSON-svaret som returnerades av visuellt innehåll när du söker efter färgschema på bilden. I det här fallet på bilden är inte en svart och vit avbildning, men dominanta färger för förgrunden och bakgrunden är svarta och dominanta färger för avbildningen som helhet är svart och vit.

![Utomhus Mountain](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Dominant färg exempel

I följande tabell beskrivs dominerande förgrund, bakgrund och bildfärger för varje exempelbild som returneras av visuellt innehåll.

| Bild | Dominanta färger |
|-------|-----------------|
|![Visuellt innehåll analyserar blommor](./Images/flower.png)| Förgrund: svart<br/>Bakgrund: vit<br/>Färger: Svart, vit, grön|
![Visuellt innehåll analyserar träna Station](./Images/train_station.png) | Förgrund: svart<br/>Bakgrund: svart<br/>Färger: svart |

### <a name="accent-color-examples"></a>Accent färg exempel

 I följande tabell beskrivs accentfärg, som ett hexadecimalt HTML-färgvärde, för varje exempelbild som returneras av visuellt innehåll.

| Bild | Accentfärg |
|-------|--------------|
|![Utomhus Mountain](./Images/mountain_vista.png) | #BB6D10 |
|![Visuellt innehåll analyserar blommor](./Images/flower.png) | #C6A205 |
|![Visuellt innehåll analyserar träna Station](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Svart och vit-exempel

I följande tabell anger om varje exempelbild är svart och vit som returneras av visuellt innehåll.

| Bild | Svartvit? |
|-------|----------------|
|![Visuellt innehåll analyserar byggnad](./Images/bw_buildings.png) | true |
|![Visuellt innehåll analyserar House Yard](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [identifiera bildtyper](concept-detecting-image-types.md).