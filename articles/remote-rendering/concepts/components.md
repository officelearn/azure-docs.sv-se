---
title: Komponenter
description: Definition av komponenter i omfånget för Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: a17bfe4dac2007d3ad136598c3c4e335e2397293
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203729"
---
# <a name="components"></a>Komponenter

Azure Remote rendering använder [enhets komponentens system](https://en.wikipedia.org/wiki/Entity_component_system) mönster. Medan [entiteter](entities.md) representerar positionen och den hierarkiska sammansättningen av objekt, är komponenter ansvariga för att implementera beteendet.

De vanligaste typerna av komponenter är [:::no-loc text="mesh components":::](meshes.md) , som lägger till nät i åter givnings pipelinen. På samma sätt används [ljusa komponenter](../overview/features/lights.md) för att lägga till belysnings-och [klipp Plans komponenter](../overview/features/cut-planes.md) som används för att klippa ut öppna maskor.

Alla dessa komponenter använder transformeringen (position, rotation, skala) för den entitet som de är kopplade till, som referens punkt.

## <a name="working-with-components"></a>Arbeta med komponenter

Du kan enkelt lägga till, ta bort och manipulera komponenter program mässigt:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

En komponent är kopplad till en entitet vid skapande tillfället. Den kan inte flyttas till en annan entitet efteråt. Komponenter tas uttryckligen bort med `Component.Destroy()` eller automatiskt när komponentens ägarnod förstörs.

Endast en instans av varje komponent typ kan läggas till i en entitet i taget.

## <a name="unity-specific"></a>Unity Specific

Union-integreringen har ytterligare utöknings bara funktioner för att interagera med komponenter. Se [Unity Game Objects and Components](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>API-dokumentation

* [C#-ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RemoteManager. CreateComponent ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.createcomponent)
* [C# entitet. FindComponentOfType ()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++-ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C++ RemoteManager:: CreateComponent ()](/cpp/api/remote-rendering/remotemanager#createcomponent)
* [C++-entitet:: FindComponentOfType ()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Nästa steg

* [Objektgränser](object-bounds.md)
* [Maskor](meshes.md)