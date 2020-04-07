---
title: Färgmaterial
description: Beskriver färgmaterialtypen.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681082"
---
# <a name="color-materials"></a>Färgmaterial

*Färgmaterial* är en av de [materialtyper](../../concepts/materials.md) som stöds i Azure Remote Rendering. De används för [maskor](../../concepts/meshes.md) som inte bör få någon form av belysning, utan snarare vara full ljusstyrka hela tiden. Detta kan vara fallet för "glödande" material, såsom bilinstrumentpaneler, glödlampor eller för data som redan innehåller statisk belysning, till exempel modeller som erhållits genom [fotogrammetri](https://en.wikipedia.org/wiki/Photogrammetry).

Färgmaterial är effektivare att göra än [PBR material](pbr-materials.md) på grund av deras enklare skuggning modell. De stöder också olika genomskinlighetslägen.

## <a name="common-material-properties"></a>Vanliga materialegenskaper

Dessa egenskaper är gemensamma för alla material:

* **albedoFärg:** Den här färgen multipliceras med andra färger, till exempel *albedoMap-* eller *vertex-färgerna*. Om *genomskinlighet* är aktiverat på ett material används alfakanalen för `1` att justera opaciteten, med full ogenomskinlig och `0` helt genomskinlig. Standard är vit.

  > [!NOTE]
  > Eftersom färgmaterial inte återspeglar miljön blir ett helt transparent färgmaterial osynligt. Detta är annorlunda för [PBR material](pbr-materials.md).

* **albedoKarta:** En [2D-textur](../../concepts/textures.md) för albedo-värden per pixel.

* **alphaClipEnabled** och **alphaClipThreshold:** Om *alphaClipEnabled* är sant, kommer alla pixlar där albedo-alfavärdet är lägre än *alphaClipThreshold* inte att dras. Alfaurklipp kan användas även utan att aktivera genomskinlighet och är mycket snabbare att rendera. Alfa klippta material är fortfarande långsammare att göra än helt ogenomskinliga material, dock. Som standard är alfaurklippet inaktiverat.

* **textureCoordinateScale** och **textureCoordinateOffset:** Skalan multipliceras med UV-texturkoordinaterna, förskjutningen läggs till den. Kan användas för att sträcka ut och flytta texturerna. Standardskalan är (1, 1) och förskjutning är (0, 0).

* **useVertexColor:** Om nätet innehåller hörnfärger och det här alternativet är aktiverat multipliceras maskornas hörnfärger i *albedoColor* och *albedoMap*. Som standard är hörnfärger inaktiverade.

* **ärDoubleSided:** Om dubbelsidighet är inställd på true, trianglar med detta material återges även om kameran tittar på deras ryggytor. Som standard är det här alternativet inaktiverat. Se även [Enkelsidig rendering](single-sided-rendering.md).

## <a name="color-material-properties"></a>Egenskaper för färgmaterial

Följande egenskaper är specifika för färgmaterial:

* **vertexMix:** Det här `0` `1` värdet mellan och anger hur starkt hörnfärgen i ett [nät](../../concepts/meshes.md) bidrar till den slutliga färgen. Vid standardvärdet 1 multipliceras vertexfärgen i albedofärgen helt. Med värdet 0 ignoreras hörnfärgerna helt.

* **öppenhetMode:** I motsats till [PBR-material](pbr-materials.md)skiljer färgmaterial mellan olika genomskinlighetslägen:

  1. **Ogenomskinlig:** Standardläget inaktiverar genomskinlighet. Alfa klippning är fortfarande möjligt, dock, och bör föredras, om det är tillräckligt.
  
  1. **AlphaBlended:** Det här läget liknar genomskinlighetsläget för PBR-material. Det bör användas för genomskinliga material som glas.

  1. **Tillsats:** Det här läget är det enklaste och mest effektiva genomskinlighetsläget. Materialets bidrag läggs till den renderade bilden. Det här läget kan användas för att simulera glödande (men ändå genomskinliga) objekt, till exempel markörer som används för att markera viktiga objekt.

## <a name="next-steps"></a>Nästa steg

* [PBR-material](pbr-materials.md)
* [Texturer](../../concepts/textures.md)
* [Maskor](../../concepts/meshes.md)
