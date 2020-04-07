---
title: Maskor
description: Definition av nät i azure remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681771"
---
# <a name="meshes"></a>Maskor

## <a name="mesh-resource"></a>Mesh-resurs

Nät är en oföränderlig [delad resurs](../concepts/lifetime.md)som bara kan skapas genom [modellkonvertering](../how-tos/conversion/model-conversion.md). Maskor innehåller en eller flera *undermåliga.* Varje undermål refererar till ett [material](materials.md) som det ska återges som standard. Om du vill placera ett nät i 3D-rymden lägger du till en [MeshComponent](#meshcomponent) i en [entitet](entities.md).

### <a name="mesh-resource-properties"></a>Egenskaper för meshresurs

Klassegenskaperna `Mesh` är:

* **Material:** En rad material. Varje material används av en annan submesh. Flera poster i matrisen kan referera till samma [material](materials.md). Dessa data kan inte ändras vid körning.

* **Gränser:** En aABB-markeringsram (local-space axis-aligned bounding box) för nättörnen.

## <a name="meshcomponent"></a>MeshComponent (MeshComponent)

Klassen `MeshComponent` används för att placera en förekomst av en nätresurs. Varje MeshComponent refererar till ett enda nät. Det kan åsidosätta vilka material som används för att återge varje undergrupp.

### <a name="meshcomponent-properties"></a>Egenskaper för MeshComponent

* **Nät:** Den nätresurs som används av den här komponenten.

* **Material:** Den mängd material som anges på själva nätkomponenten. Matrisen har alltid samma längd som matrisen *Material* på nätresursen. Material som inte får åsidosättas från nätstandarden är inställda på *null* i den här matrisen.

* **UsedMaterials:** Matrisen med faktiskt använda material för varje undernäring. Kommer att vara identisk *Materials* med data i materialmatrisen, för icke-null-värden. Annars innehåller det värdet *Materials* från materialmatrisen i nätinstansen.

## <a name="next-steps"></a>Nästa steg

* [Material](materials.md)
