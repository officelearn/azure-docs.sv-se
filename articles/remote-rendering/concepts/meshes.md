---
title: Maskor
description: Definition av nät i omfånget för Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 08d80a5ec2099147c12bcecd3b52d64429837285
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563972"
---
# <a name="meshes"></a>Maskor

## <a name="mesh-resource"></a>Nät resurs

Maskor är en oföränderlig [delad resurs](../concepts/lifetime.md)som bara kan skapas via [modell konvertering](../how-tos/conversion/model-conversion.md). Maskor innehåller ett eller flera under *nät* tillsammans med en fysik representation för raycast- [frågor](../overview/features/spatial-queries.md). Varje under nät refererar till ett [material](materials.md) med vilket det ska återges som standard. Lägg till en [MeshComponent](#meshcomponent) till en [entitet](entities.md)för att placera ett nät i 3D-rymden.

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

### <a name="sharing-of-meshes"></a>Delning av nät

En `Mesh` resurs kan delas över flera instanser av nät komponenter. Dessutom kan den `Mesh` resurs som är tilldelad en nät komponent ändras program mässigt när som helst. Koden nedan visar hur du klonar ett nät:

```cs
Entity CloneEntityWithModel(RemoteManager manager, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = manager.CreateEntity();
        MeshComponent newMeshComp = manager.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RemoteManager> manager, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *manager->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = manager->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API-dokumentation

* [C#, nät klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C++, nät klass](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [C++ MeshComponent-klass](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Nästa steg

* [Material](materials.md)
