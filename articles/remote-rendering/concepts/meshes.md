---
title: Maskor
description: Definition av nät i omfånget för Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: eea43f48abef5e2b258251d46eca1061a2263519
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613841"
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

## <a name="api-documentation"></a>API-dokumentation

* [C#, nät klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C++, nät klass](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [C++ MeshComponent-klass](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)

## <a name="next-steps"></a>Nästa steg

* [Material](materials.md)
