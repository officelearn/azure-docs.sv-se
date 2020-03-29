---
title: Färgschemaidentifiering - Datorseende
titleSuffix: Azure Cognitive Services
description: Begrepp relaterade till att identifiera färgschemat i bilder med hjälp av API:et för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244740"
---
# <a name="detect-color-schemes-in-images"></a>Identifiera färgscheman i bilder

Computer Vision analyserar färgerna i en bild för att ge tre olika attribut: den dominerande förgrundsfärgen, den dominerande bakgrundsfärgen och uppsättningen dominerande färger för bilden som helhet. Returnerade färger tillhör uppsättningen: svart, blå, brun, grå, grön, orange, rosa, lila, röd, kricka, vit och gul. 

Computer Vision extraherar också en accentfärg, som representerar den mest levande färgen i bilden, baserat på en kombination av dominerande färger och mättnad. Accentfärgen returneras som en hexadecimal HTML-färgkod. 

Datorseende returnerar också ett booleskt värde som anger om en bild är i svartvitt.

## <a name="color-scheme-detection-examples"></a>Exempel på identifiering av färgschema

I följande exempel visas JSON-svaret som returneras av Datorseende när exempelbildens färgschema identifieras. I det här fallet är exempelbilden inte en svartvit bild, men de dominerande förgrunds- och bakgrundsfärgerna är svarta och de dominerande färgerna för bilden som helhet är svartvita.

![Utomhusberg vid solnedgången, med en persons silhuett](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Dominerande färgexempel

I följande tabell visas de returnerade förgrunds-, bakgrunds- och bildfärgerna för varje exempelbild.

| Bild | Dominerande färger |
|-------|-----------------|
|![En vit blomma med grön bakgrund](./Images/flower.png)| Förgrund: Svart<br/>Bakgrund: Vit<br/>Färger: Svart, Vit, Grön|
![Ett tåg som går genom en station](./Images/train_station.png) | Förgrund: Svart<br/>Bakgrund: Svart<br/>Färger: Svart |

### <a name="accent-color-examples"></a>Exempel på accentfärg

 I följande tabell visas den returnerade accentfärgen som ett hexadecimalt HTML-färgvärde för varje exempelbild.

| Bild | Accentfärg |
|-------|--------------|
|![En person som står på en bergsten vid solnedgången](./Images/mountain_vista.png) | #BB6D10 |
|![En vit blomma med grön bakgrund](./Images/flower.png) | #C6A205 |
|![Ett tåg som går genom en station](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exempel på svart & vit identifiering

I följande tabell visas Datorseendes svartvita utvärdering i exempelbilderna.

| Bild | Svart & vit? |
|-------|----------------|
|![En svartvit bild av byggnader på Manhattan](./Images/bw_buildings.png) | true |
|![Ett blått hus och framsidan](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>Använda API:et

Färgschemaidentifieringsfunktionen är en del av [Api:et för analyseravbildning.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Du kan anropa det här API:et via en inbyggd SDK eller via REST-anrop. Inkludera `Color` i frågeparametern **visualFeatures.** Sedan, när du får hela JSON svar, helt enkelt tolka `"color"` strängen för innehållet i avsnittet.

* [Snabbstart: Datorseende .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)
