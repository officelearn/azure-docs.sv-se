---
title: Generera en miniatyr bild av Sprite med Azure Media Services | Microsoft Docs
description: Det här avsnittet visar hur du genererar en miniatyr av en miniatyr bild med Azure Media Services.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "61229058"
---
# <a name="generate-a-thumbnail-sprite"></a>Skapa en miniatyrsprite  

Du kan använda Media Encoder Standard för att generera en miniatyr av en miniatyr bild, som är en JPEG-fil som innehåller flera små upplöst miniatyr bilder som häftas samman i en enda (stor) bild, tillsammans med en VTT-fil. Den här VTT-filen anger tidsintervallet i indata-videon som varje miniatyr representerar, tillsammans med storlek och koordinater för miniatyren i den stora JPEG-filen. Videos pelare använder VTT-filen och Sprite-bilden för att visa en "visuell" SeekBar, vilket ger ett visnings program med visuell feedback vid skrubbning och vidarebefordran längs video tids linjen.

För att kunna använda Media Encoder Standard för att generera miniatyr sprit, är för inställningen:

1. Bild formatet för JPG-miniatyr måste användas
2. Du måste ange värden för start/steg/intervall som antingen tidsstämplar eller% Values (och inte antal bild rutor) 
    
    1. Det går att blanda tidsstämplar och% Values

3. Måste ha värdet SpriteColumn, som ett icke-negativt tal som är större än eller lika med 1

    1. Om SpriteColumn är inställt på M >= 1, är utmatnings bilden en rektangel med M-kolumner. Om antalet miniatyrer som genereras via #2 inte är en exakt multipel av M, kommer den sista raden att vara ofullständig och lämna kvar med svarta bild punkter.  

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

1.  Det går inte att skapa en sprite-avbildning med en enda rad med bilder (SpriteColumn = 1 resulterar i en bild med en enda kolumn).
2.  Det finns inte stöd för att dela upp Sprite-bilderna i stora JPEG-bilder. Därför måste det vara viktigt att begränsa antalet miniatyrer och deras storlek, så att den resulterande sammanslagna miniatyr bilden blir runt 8 M pixlar eller mindre.
3.  Azure Media Player stöder spriter i webbläsare för Microsoft Edge, Chrome och Firefox. VTT-parsning stöds inte i IE11.

## <a name="next-steps"></a>Nästa steg

[Koda innehåll](media-services-encode-asset.md)
