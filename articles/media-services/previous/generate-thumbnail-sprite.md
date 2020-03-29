---
title: Generera en miniatyrsprit med Azure Media Services | Microsoft-dokument
description: Det här avsnittet visar hur du skapar en miniatyrsprit med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61229058"
---
# <a name="generate-a-thumbnail-sprite"></a>Skapa en miniatyrsprite  

Du kan använda Media Encoder Standard för att generera en miniatyrsprit, som är en JPEG-fil som innehåller flera miniatyrer med liten upplösning som sytt ihop till en enda (stor) bild, tillsammans med en VTT-fil. Den här VTT-filen anger tidsintervallet i indatavideon som varje miniatyr bild representerar, tillsammans med storleken och koordinaterna för miniatyrbilden i den stora JPEG-filen. Videospelare använder VTT-filen och sprite-bilden för att visa en "visuell" sökbar, vilket ger en tittare visuell feedback när de skrubbar fram och tillbaka längs videotidslinjen.

För att kunna använda Media Encoder Standard för att generera Thumbnail Sprite, förinställningen:

1. Måste använda JPG-miniatyrbildformat
2. Måste ange värden för Start/Steg/Intervall som antingen tidsstämplar eller % -värden (och inte bildruteantal) 
    
    1. Det är okej att blanda tidsstämplar och % värden

3. Måste ha SpriteColumn-värdet, som ett icke-negativt tal som är större än eller lika med 1

    1. Om SpriteColumn är inställt på M >= 1 är utdatabilden en rektangel med M-kolumner. Om antalet miniatyrer som genereras via #2 inte är en exakt multipel av M, kommer den sista raden att vara ofullständig och lämnas med svarta pixlar.  

Här är ett exempel:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Kända problem

1.  Det går inte att generera en sprite-bild med en enda rad bilder (SpriteColumn = 1 resulterar i en bild med en enda kolumn).
2.  Chunking av sprite bilder till måttligt stora JPEG-bilder stöds inte ännu. Därför måste man vara noga med att begränsa antalet miniatyrer och deras storlek, så att den resulterande sydda Thumbnail Sprite är cirka 8M pixlar eller mindre.
3.  Azure Media Player stöder sprites i Microsoft Edge-, Chrome- och Firefox-webbläsare. VTT-tolkning stöds inte i IE11.

## <a name="next-steps"></a>Nästa steg

[Koda innehåll](media-services-encode-asset.md)
