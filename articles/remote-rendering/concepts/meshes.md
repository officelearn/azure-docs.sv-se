---
title: Maskor
description: Definition av nät i omfånget för Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681771"
---
# <a name="meshes"></a>Maskor

## <a name="mesh-resource"></a>Nät resurs

Maskor är en oföränderlig [delad resurs](../concepts/lifetime.md)som bara kan skapas via [modell konvertering](../how-tos/conversion/model-conversion.md). Maskor innehåller ett eller flera under *nät*. Varje under nät refererar till ett [material](materials.md) med vilket det ska återges som standard. Lägg till en [MeshComponent](#meshcomponent) till en [entitet](entities.md)för att placera ett nät i 3D-rymden.

### <a name="mesh-resource-properties"></a>Egenskaper för nät resurs

`Mesh`Klass egenskaperna är:

* **Material:** En matris med material. Varje material används av ett annat under nät. Flera poster i matrisen kan referera till samma [material](materials.md). Dessa data kan inte ändras vid körning.

* **Gränser:** En axel med lokalt blank steg (AABB) för nät hörnen.

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`Klassen används för att placera en instans av en nät resurs. Varje MeshComponent hänvisar till ett enda nät. Det kan åsidosätta vilka material som används för att återge varje under nät.

### <a name="meshcomponent-properties"></a>Egenskaper för MeshComponent

* **Nät:** Den nät resurs som används av den här komponenten.

* **Material:** Den matris med material som anges i själva nät komponenten. Matrisen kommer alltid att ha samma längd som *material* mat ris i nät resursen. Material som inte ska åsidosättas från standardvärdet för nät är inställt på *Null* i matrisen.

* **UsedMaterials:** Matrisen med faktiskt använt material för varje under nät. Är identisk med data i *material* mat ris för värden som inte är null. Annars innehåller den värdet från *material* mat ris i nät instansen.

## <a name="next-steps"></a>Nästa steg

* [Material](materials.md)
