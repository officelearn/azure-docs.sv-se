---
title: Objektgränser
description: Förklarar hur spatial objekts gränser kan frågas
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 099b0175b906e48034e663b575517e49bce7e753
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021858"
---
# <a name="object-bounds"></a>Objektgränser

Objekt gränser representerar den volym som en [entitet](entities.md) och dess underordnade upptar. I Azure fjärrrendering anges objekt gränser alltid som *axel justerings* ramar (AABB). Objekt gränser kan vara antingen i *lokalt utrymme* eller i *hela utrymmet*. Oavsett sätt är de alltid axelbaserade, vilket innebär att lagrings utrymmet och volymen kan skilja sig åt mellan den lokala och det dagliga utrymmes representationen.

## <a name="querying-object-bounds"></a>Frågar efter objekt gränser

Den lokala AABB för ett [nät](meshes.md) kan frågas direkt från nät resursen. Dessa gränser kan omvandlas till ett lokalt utrymme eller ett globalt utrymme för en entitet som använder entitetens Transform.

Det är möjligt att beräkna gränserna för en hel Object-hierarki på det här sättet, men det kräver att du går igenom hierarkin, frågar gränserna för varje nät och kombinerar dem manuellt. Den här åtgärden är både omständlig och ineffektiv.

Ett bättre sätt är att anropa `QueryLocalBoundsAsync` eller `QueryWorldBoundsAsync` på en entitet. Beräkningen avlastas sedan till servern och returneras med minimal fördröjning.

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>Nästa steg

* [Rumsliga frågor](../overview/features/spatial-queries.md)
