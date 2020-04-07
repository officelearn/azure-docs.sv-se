---
title: Material
description: Beskrivning av renderingsmaterial och materialegenskaper
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681849"
---
# <a name="materials"></a>Material

Material är [delade resurser](../concepts/lifetime.md) som definierar hur [nät](meshes.md) återges. Material används för att ange vilka [texturer](textures.md) som ska tillämpas, om objekt ska genomskinliga och hur belysning ska beräknas.

Material skapas automatiskt under [modellkonvertering](../how-tos/conversion/model-conversion.md) och är tillgängliga vid körning. Du kan också skapa anpassat material från kod och ersätta befintliga. Det här scenariot är särskilt logiskt om du vill dela samma material över många nät. Eftersom ändringar av ett material är synliga på varje nät som refererar till det, kan denna metod användas för att enkelt tillämpa ändringar.

> [!NOTE]
> Vissa användningsfall, till exempel att markera ett plockat objekt, kan göras genom att ändra material, men är mycket enklare att uppnå via [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Materialtyper

Azure Remote Rendering har två olika materialtyper:

* [PBR-material](../overview/features/pbr-materials.md) används för ytor som ska återges så fysiskt korrekta som möjligt. Realistisk belysning beräknas för dessa material med hjälp av *fysiskt baserad rendering* (PBR). För att få ut det mesta av denna materialtyp är det viktigt att tillhandahålla högkvalitativa indata, till exempel ojämnheter och normala kartor.

* [Färgmaterial](../overview/features/color-materials.md) används för fall där ingen ytterligare belysning önskas. Dessa material är alltid fullt ljusa och är lättare att ställa in. Färgmaterial används för data som antingen inte ska ha någon belysning alls, eller redan innehåller statisk belysning, till exempel modeller som erhållits genom [fotogrammetri](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Tilldelning av mesh jämfört med MeshComponent material

[Maskor](meshes.md) har en eller flera undermåliga. Varje undernäring refererar till ett material. Du kan ändra materialet så att det används direkt på nätet eller åsidosätta vilket material som ska användas för en undergrupp på en [MeshComponent](meshes.md#meshcomponent).

När du ändrar ett material direkt på nätresursen påverkar den här ändringen alla instanser av det nätet. Om du ändrar den på MeshComponent påverkas den dock bara av en nätinstans. Vilken metod som är lämpligare beror på önskat beteende, men att ändra en MeshComponent är den vanligaste metoden.

## <a name="material-classes"></a>Materialklasser

Allt material som tillhandahålls av API:et kommer från basklassen `Material`. Deras typ kan efterfrågas genom `Material.MaterialSubType` eller genom att casta dem direkt:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>Nästa steg

* [PBR-material](../overview/features/pbr-materials.md)
* [Färgmaterial](../overview/features/color-materials.md)
