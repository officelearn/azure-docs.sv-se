---
title: Identifiering av färg schema – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör att identifiera färg schema i bilder med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e0fa85b8a90ea57d9b81bd2eeaa6d080b7582acd
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945283"
---
# <a name="detect-color-schemes-in-images"></a>Identifiera färg scheman i bilder

Visuellt innehåll analyserar färgerna i en bild för att tillhandahålla tre olika attribut: den dominerande förgrunds färgen, den dominerande bakgrunds färgen och uppsättningen av dominerande färger för bilden som helhet. Returnerade färger tillhör uppsättningen: svart, blå, brun, grå, grön, orange, rosa, lila, röd, blågrön, vit och gul. 

Visuellt innehåll också extrahera en accentfärg, som representerar den mest livfulla färgen i bilden, baserat på en kombination av dominerande färger och mättnad. Dekor färgen returneras som en hexadecimal HTML-färgkod. 

Visuellt innehåll returnerar också ett booleskt värde som anger om en bild är i svart vitt.

## <a name="color-scheme-detection-examples"></a>Exempel på identifiering av färg scheman

I följande exempel visas ett JSON-svar som returneras av Visuellt innehåll när du identifierar exempel bildens färg schema. I det här fallet är exempel bilden inte en svartvit bild, men de dominerande förgrunds-och bakgrunds färgerna är svarta och de dominerande färgerna för bilden som helhet är svarta och vita.

![Utomhus mountainbike vid solnedgång, med en persons Silhouette](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Dominerande färg exempel

I följande tabell visas de färger som returneras för förgrunds-, bakgrunds-och bildfiler för varje exempel bild.

| Image | Dominanta färger |
|-------|-----------------|
|![En vit blomma med grön bakgrund](./Images/flower.png)| Överst Svart<br/>Lägg Vit<br/>Färg Svart, vit, grön|
![Ett tåg som körs via en station](./Images/train_station.png) | Överst Svart<br/>Lägg Svart<br/>Färg Svart |

### <a name="accent-color-examples"></a>Dekor färgs exempel

 I följande tabell visas den returnerade tilläggs färgen, som ett hexadecimalt HTML-färg värde, för varje exempel bild.

| Image | Accentfärg |
|-------|--------------|
|![En person som står på en Mountain rock vid solnedgång](./Images/mountain_vista.png) | #BB6D10 |
|![En vit blomma med grön bakgrund](./Images/flower.png) | #C6A205 |
|![Ett tåg som körs via en station](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exempel på svart & White Detection

I följande tabell visas Visuellt innehållens svarta och vita utvärdering i exempel bilderna.

| Image | Svart & vitt? |
|-------|----------------|
|![En svart och vit bild av byggnader i Manhattan](./Images/bw_buildings.png) | true |
|![Ett blått hus och en främre meter](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om att [identifiera avbildnings typer](concept-detecting-image-types.md).
