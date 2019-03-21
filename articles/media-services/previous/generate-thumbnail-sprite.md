---
title: Generera en miniatyr sprite med Azure Media Services | Microsoft Docs
description: Det här avsnittet visar hur du skapar en miniatyr sprite med Azure Media Services.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991655"
---
# <a name="generate-a-thumbnail-sprite"></a>Generera en miniatyr sprite  

Du kan använda Media Encoder Standard för att generera miniatyrbilder sprite, vilket är en JPEG-fil som innehåller flera små upplösning miniatyrer stitched tillsammans i en enda (stor) avbildning, tillsammans med en VTT-fil. Den här VTT-filen anger tidsintervallet i indatavideon som representerar varje miniatyr, tillsammans med den storlek och koordinaterna för den miniatyren i stora JPEG-filer. Videospelare använda VTT fil- och sprite avbildningar för att visa en 'visual' seekbar att erbjuda ett visningsprogram visuell feedback när Skrubba tillbaka och vidarebefordra längs video tidslinjen.

För att kunna använda Media Encoder Standard för att generera miniatyrbilden Sprite, förinställningen:

1. Måste använda miniatyrbilden JPG-format
2. Ange Start/steg/omfångsvärden som antingen tidsstämplar eller % värden (och inte ramens antal) 
    
    1. Det är Okej att blanda tidsstämplar och % värden

3. Måste ha SpriteColumn-värde som ett negativt tal större än eller lika med 1

    1. Om SpriteColumn anges till M > = 1, utdata-avbildningen är en rektangel med M-kolumner. Om antalet miniatyrer som genererats via #2 inte är en exakt multipel av M, blir den sista raden ofullständig och vänstra med svart bildpunkter.  

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

1.  Det går inte att generera en sprite-avbildning med en enda rad med bilder (SpriteColumn = 1 resultat i en bild med en enda kolumn).
2.  Dela upp sprite avbildning till mindre storlek JPEG-bilder stöds inte ännu. Därför måste du vara noggrann begränsar antalet miniatyrer och deras storlek, så att den resulterande stitched miniatyr Sprite är runt 8 M bildpunkter eller mindre.
3.  Azure Media Player stöder spriter i Microsoft Edge, Chrome och Firefox webbläsare. Parsning av VTT stöds inte i IE 11.

## <a name="next-steps"></a>Nästa steg

[Koda innehåll](media-services-encode-asset.md)
