---
title: Färgmaterial
description: Beskriver färg Materials typen.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: cda4aa9a811bac0ccf20caec32ee38da9b46b6c7
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613769"
---
# <a name="color-materials"></a>Färgmaterial

*Färg material* är en av de [material typer](../../concepts/materials.md) som stöds i Azure Remote rendering. De används för [nät](../../concepts/meshes.md) som inte får ta emot någon typ av belysning, utan alltid full ljus styrka. Detta kan vara fallet för "glödande" material, t. ex. bil instrument paneler, glöd lampor eller för data som redan har en statisk belysning, till exempel modeller som erhållits via [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

Färg material är mer effektivt att rendera än [PBR-material](pbr-materials.md) på grund av sin enklare skuggnings modell. De har också stöd för olika genomskinlighets lägen.

## <a name="common-material-properties"></a>Vanliga material egenskaper

Dessa egenskaper är gemensamma för allt material:

* **albedoColor:** Den här färgen multipliceras med andra färger, till exempel *albedoMap* eller * :::no-loc text="vertex"::: färger*. Om *transparens* är aktiverat på ett material används alfa kanalen för att justera opaciteten, med en `1` helt ogenomskinlig och innebörd som är `0` helt transparent. Standardvärdet är White.

  > [!NOTE]
  > Eftersom färg material inte återspeglar miljön blir ett helt transparent färg material osynligt. Detta skiljer sig för [PBR-material](pbr-materials.md).

* **albedoMap:** En [2D-struktur](../../concepts/textures.md) för albedo-värden per pixel.

* **alphaClipEnabled** och **AlphaClipThreshold:** om *alphaClipEnabled* är true, kommer alla pixlar där albedo alpha-värdet är lägre än *alphaClipThreshold* att ritas. Alfa Urklipp kan användas även utan att aktivera genomskinlighet och är mycket snabbare att återge. Alfa urklippta material är fortfarande långsammare att rendera än helt ogenomskinligt material, även om. Som standard är alpha-Urklipp inaktiverat.

* **textureCoordinateScale** och **textureCoordinateOffset:** skalan multipliceras med UV-texturens koordinater. förskjutningen läggs till i den. Kan användas för att sträcka ut och byta texturer. Standard skalan är (1, 1) och förskjutningen är (0, 0).

* **useVertexColor:** Om nätet innehåller :::no-loc text="vertex"::: färger och det här alternativet är aktiverat :::no-loc text="vertex"::: multipliceras maskens färg med *albedoColor* och *albedoMap*. Som standard är *useVertexColor* inaktive rad.

* **isDoubleSided:** Om Double-sidedness är inställt på Sant återges trianglar med det här materialet även om kameran tittar på bak sidorna. Som standard är det här alternativet inaktiverat. Se även [ :::no-loc text="Single-sided"::: åter givning](single-sided-rendering.md).

## <a name="color-material-properties"></a>Egenskaper för färg material

Följande egenskaper är speciella för färg material:

* **vertexMix:** Det här värdet mellan `0` och `1` anger hur starkt :::no-loc text="vertex"::: färgen i ett [nät](../../concepts/meshes.md) bidrar till den slutliga färgen. Med standardvärdet 1 :::no-loc text="vertex"::: multipliceras färgen med albedo-färgen helt. Med värdet 0 :::no-loc text="vertex"::: ignoreras färgerna helt.

* **transparencyMode:** Till skillnad från [PBR-material](pbr-materials.md)skiljer sig färg material mellan olika genomskinlighets lägen:

  1. **Ogenomskinlig:** Standard läget inaktiverar transparens. Alfa Urklipp är fortfarande möjligt, men bör helst, och bör helst, om det behövs.
  
  1. **AlphaBlended:** Det här läget liknar genomskinlighets läget för PBR-material. Den bör användas för att se på material som glas.

  1. **Tillsats:** Det här läget är det enklaste och mest effektiva genomskinlighets läget. Material bidraget läggs till i den återgivna avbildningen. Det här läget kan användas för att simulera glödande (men fortfarande genomskinliga) objekt, till exempel markörer som används för att markera viktiga objekt.

## <a name="api-documentation"></a>API-dokumentation

* [C# ColorMaterial-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RemoteManager. CreateMaterial ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [C++ ColorMaterial-klass](https://docs.microsoft.com/cpp/api/remote-rendering/colormaterial)
* [C++ RemoteManager:: CreateMaterial ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Nästa steg

* [PBR-material](pbr-materials.md)
* [Bakgrunder](../../concepts/textures.md)
* [Maskor](../../concepts/meshes.md)
