---
title: Objekt bounds
description: Förklarar hur rumsliga objekt bounds kan efterfrågas
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681719"
---
# <a name="object-bounds"></a>Objekt bounds

Objekt bounds representerar den volym som en [entitet](entities.md) och dess underordnade upptar. I Azure Remote Rendering anges alltid objekt bounds som *axeljusterade markeringsrutor* (AABB). Objekt bounds kan vara antingen i *lokalt utrymme* eller i *världen rymden*. Hursomhelst, de är alltid axel-anpassade, vilket innebär att omfattningen och volymen kan skilja sig mellan den lokala och globala rymdrepresentation.

## <a name="querying-object-bounds"></a>Att fråga objekt bounds

Den lokala AABB för ett [nät](meshes.md) kan efterfrågas direkt från nätresursen. Dessa gränser kan omvandlas till det lokala utrymmet eller världsrymden för en entitet med hjälp av entitetens transformering.

Det är möjligt att beräkna gränserna för en hel objekthierarki på det här sättet, men det kräver att du korsar hierarkin, frågar gränserna för varje nät och kombinerar dem manuellt. Denna operation är både tråkig och ineffektiv.

Ett bättre sätt `QueryLocalBoundsAsync` är `QueryWorldBoundsAsync` att ringa eller på en entitet. Beräkningen lastas sedan av till servern och returneras med minimal fördröjning.

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
