---
title: Komponenter
description: Definition av komponenter i Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681901"
---
# <a name="components"></a>Komponenter

Azure Remote Rendering använder mönstret [entity component system.](https://en.wikipedia.org/wiki/Entity_component_system) Medan [entiteter representerar](entities.md) position och hierarkisk sammansättning av objekt, är komponenter ansvariga för att implementera beteende.

De vanligaste typerna av komponenter är [nätkomponenter](meshes.md), som lägger till maskor i renderingspipelinen. På samma sätt används [lätta komponenter](../overview/features/lights.md) för att lägga till belysning och [skärplanskomponenter](../overview/features/cut-planes.md) för att skära upp nät.

Alla dessa komponenter använder transformeringen (position, rotation, skala) för den entitet de är kopplade till som referenspunkt.

## <a name="working-with-components"></a>Arbeta med komponenter

Du kan enkelt lägga till, ta bort och manipulera komponenter programmässigt:

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

En komponent är kopplad till en entitet vid skapande. Det går inte att flytta till en annan enhet efteråt. Komponenter tas uttryckligen `Component.Destroy()` bort med eller automatiskt när komponentens ägarentitet förstörs.

Endast en instans av varje komponenttyp kan läggas till i en entitet åt gången.

## <a name="unity-specific"></a>Enhetsspecifik

Unity-integrationen har ytterligare tilläggsfunktioner för interaktion med komponenter. Se [Unity spelobjekt och komponenter](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Nästa steg

* [Objekt bounds](object-bounds.md)
* [Maskor](meshes.md)
