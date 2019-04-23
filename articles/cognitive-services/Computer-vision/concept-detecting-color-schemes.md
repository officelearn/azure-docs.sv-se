---
title: Identifiera färgscheman - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för identifiering av färgschemat i bilder med den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ff7af2204f9e05a1ba4ef800c63c3ad462242350
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998625"
---
# <a name="detect-color-schemes-in-images"></a>Identifiera färgscheman i bilder

Visuellt innehåll analyserar färgerna i en bild som tillhandahåller tre olika attribut: dominerande förgrundsfärgen, dominerande bakgrundsfärgen och uppsättning dominanta färger för avbildningen som helhet. Returnerade färger som tillhör uppsättningen: svart, blå, brown, grå, grön, orange, rosa, lila, röd, mörkturkos, vit och gul. 

Visuellt innehåll extraherar också en accentfärg som representerar den mest levande färgen i bilden, baserat på en kombination av dominanta färger och mättnad. Accentfärg returneras som en hexadecimal färgkod i HTML. 

Visuellt innehåll returnerar också ett booleskt värde som anger om en bild är Svartvit.

## <a name="color-scheme-detection-examples"></a>Färgschema identifiering exempel

I följande exempel visas JSON-svaret som returnerades av visuellt innehåll när du söker efter färgschema på bilden. I det här fallet på bilden är inte en svart och bild, men dominerande förgrunds- och bakgrundsfärger är svarta och dominanta färger för avbildningen som helhet är svart och vit.

![Anpassad för utomhusbruk Mountain på sunset med en persons silhuett](./Images/mountain_vista.png)

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

I följande tabell visas den returnerade förgrund, bakgrund och avbildning färger för varje exempelbild.

| Image | Dominanta färger |
|-------|-----------------|
|![En vit blommor med en grön bakgrund](./Images/flower.png)| Förgrund: Svart<br/>Bakgrund: Vit<br/>Färger: Black, White, Green|
![Ett flygplan som körs via en station](./Images/train_station.png) | Förgrund: Svart<br/>Bakgrund: Svart<br/>Färger: Svart |

### <a name="accent-color-examples"></a>Accent färg exempel

 I följande tabell visas de returnerade accentfärg som ett hexadecimalt HTML-färgvärde, för varje exempelbild.

| Image | Accentfärg |
|-------|--------------|
|![En person som står på en mountain rock på solnedgång](./Images/mountain_vista.png) | #BB6D10 |
|![En vit blommor med en grön bakgrund](./Images/flower.png) | #C6A205 |
|![Ett flygplan som körs via en station](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Svart och vit-exempel

I följande tabell visas datorn Vision svart och utvärdering i exempelbilderna.

| Image | Svartvit? |
|-------|----------------|
|![En svart och bild av byggnader i Manhattan](./Images/bw_buildings.png) | true |
|![En blå house och framsida](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [identifiera bildtyper](concept-detecting-image-types.md).
