---
title: Objektgränser
description: Förklarar hur spatial objekts gränser kan frågas
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681719"
---
# <a name="object-bounds"></a>Objektgränser

Objekt gränser representerar den volym som en [entitet](entities.md) och dess underordnade upptar. I Azure fjärrrendering anges objekt gränser alltid som *axel justerings* ramar (AABB). Objekt gränser kan vara antingen i *lokalt utrymme* eller i *hela utrymmet*. Oavsett sätt är de alltid axelbaserade, vilket innebär att lagrings utrymmet och volymen kan skilja sig åt mellan den lokala och det dagliga utrymmes representationen.

## <a name="querying-object-bounds"></a>Frågar efter objekt gränser

Den lokala AABB för ett [nät](meshes.md) kan frågas direkt från nät resursen. Dessa gränser kan omvandlas till ett lokalt utrymme eller ett globalt utrymme för en entitet som använder entitetens Transform.

Det är möjligt att beräkna gränserna för en hel Object-hierarki på det här sättet, men det kräver att du går igenom hierarkin, frågar gränserna för varje nät och kombinerar dem manuellt. Den här åtgärden är både omständlig och ineffektiv.

Ett bättre sätt är att anropa `QueryLocalBoundsAsync` eller `QueryWorldBoundsAsync` på en entitet. Beräkningen avlastas sedan till servern och returneras med minimal fördröjning.

``` cs
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

## <a name="next-steps"></a>Nästa steg

* [Rumsliga frågor](../overview/features/spatial-queries.md)
