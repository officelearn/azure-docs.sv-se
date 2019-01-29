---
title: Identifiera färgscheman - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för identifiering av färgschemat i bilder med den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d882ad89e68936d07ae4d76218c6e3ac450185a8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151336"
---
# <a name="detect-color-schemes-in-images"></a>Identifiera färgscheman i bilder

Visuellt innehåll extraherar färger från en avbildning. Färgerna analyseras sedan i tre olika kontexter: dominerande förgrundsfärgen dominerande bakgrundsfärgen och dominanta färger för avbildningen som helhet. De grupperas i 12 dominanta accentfärger. Dessa accentfärger är svarta, blå, brown, grå, grön, orange, rosa, lila, röd, mörkturkos, vit och gul. Visuellt innehåll analyserar de färger som extraheras från en avbildning för att returnera en accentfärg som representerar den mest levande färgen för bilden till användarna genom en kombination av dominanta färger och mättnad. Beroende på färgerna i en bild returneras enkel svartvit eller accentfärger i hexadecimal-färgkoder. Visuellt också returnerar ett booleskt värde som anger om en bild är svart och vit.

## <a name="color-scheme-detection-examples"></a>Färgschema identifiering exempel

I följande exempel visas JSON-svaret som returnerades av visuellt innehåll när du söker efter färgschema på bilden. I det här fallet på bilden är inte en svart och vit avbildning, men dominanta färger för förgrunden och bakgrunden är svarta och dominanta färger för avbildningen som helhet är svart och vit.

![Berg utomhus](./Images/mountain_vista.png)

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
|![En vit blommor med en grön bakgrund](./Images/flower.png)| Förgrund: Svart<br/>Bakgrund: Vit<br/>Färger: Black, White, Green|
![Ett flygplan som körs via en station](./Images/train_station.png) | Förgrund: Svart<br/>Bakgrund: Svart<br/>Färger: Svart |

### <a name="accent-color-examples"></a>Accent färg exempel

 I följande tabell beskrivs accentfärg, som ett hexadecimalt HTML-färgvärde, för varje exempelbild som returneras av visuellt innehåll.

| Bild | Accentfärg |
|-------|--------------|
|![En person som står på en mountain rock på solnedgång](./Images/mountain_vista.png) | #BB6D10 |
|![En vit blommor med en grön bakgrund](./Images/flower.png) | #C6A205 |
|![Ett flygplan som körs via en station](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Svart och vit-exempel

I följande tabell anger om varje exempelbild är svart och vit som returneras av visuellt innehåll.

| Bild | Svartvit? |
|-------|----------------|
|![En svart och bild av byggnader i Manhattan](./Images/bw_buildings.png) | true |
|![En blå house och framsida](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [identifiera bildtyper](concept-detecting-image-types.md).
