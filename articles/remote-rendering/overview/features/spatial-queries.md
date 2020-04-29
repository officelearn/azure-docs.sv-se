---
title: Rumsliga frågor
description: Så här gör du spatiala frågor i en scen
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680536"
---
# <a name="spatial-queries"></a>Rumsliga frågor

Spatiala frågor är åtgärder med vilka du kan ställa en fråga till tjänsten för fjärrrendering som objekt finns i ett-ställe. Rums frågor används ofta för att implementera interaktioner, till exempel för att räkna ut vilka objekt en användare pekar på.

Alla spatiala frågor utvärderas på servern. De är därför asynkrona åtgärder och resultat kommer att få en fördröjning som beror på din nätverks fördröjning. Eftersom varje spatial fråga genererar nätverks trafik bör du vara noga med att inte göra för många samtidigt.

## <a name="collision-meshes"></a>Kollisions nät

Spatiala frågor drivs av [Havok fysik](https://www.havok.com/products/havok-physics) motor och kräver att ett dedikerat kollisions nät finns. Som standard genererar [modell konverteringen](../../how-tos/conversion/model-conversion.md) kollisions nät. Om du inte behöver spatiala frågor på en komplex modell bör du överväga att inaktivera kollision av kollision i [konverterings alternativen](../../how-tos/conversion/configure-model-conversion.md), eftersom det påverkar flera olika sätt:

* [Modell konverteringen](../../how-tos/conversion/model-conversion.md) tar avsevärt längre tid.
* Konverterade modell fil storlekar är märkbart större, vilket påverkar nedladdnings hastigheten.
* Tiden för inläsning av körnings tid är längre.
* Processor förbrukningen för körnings processor är högre.
* Det finns en mindre belastning för körnings prestanda för varje modell instans.

## <a name="ray-casts"></a>Ray-sändningar

En *Ray-Cast* är en rums fråga där körningen kontrollerar vilka objekt som skärs av en Ray, med början vid en viss position och pekar på en viss riktning. Som en optimering ges ett maximalt Ray-avstånd också för att inte söka efter objekt som är för långt bort.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

Det finns tre lägen för träff insamling:

* **Närmast:** I det här läget rapporteras endast den närmaste träffen.
* **Valfritt:** Föredra det här läget när allt du vill veta är *om* en Ray skulle träffa något, men var inte noga med vad som trycks exakt. Den här frågan kan vara avsevärt billigare att utvärdera, men har även bara några få program.
* **Alla:** I det här läget rapporteras alla träffar längs Rayen, sorterade efter avstånd. Använd inte det här läget om du inte verkligen behöver mer än den första träffen. Begränsa antalet rapporterade träffar med `MaxHits` alternativet.

Om du vill utesluta objekt selektivt från att beaktas för Ray-sändningar kan [HierarchicalStateOverrideComponent](override-hierarchical-state.md) -komponenten användas.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Träff resultat

Resultatet av en Ray Cast-fråga är en matris med träffar. Matrisen är tom, om inget objekt träffades.

En träff har följande egenskaper:

* **HitEntity:** Vilken [entitet](../../concepts/entities.md) som träffades.
* **Delpartis:** Vilket under *nätet* nåddes i en [MeshComponent](../../concepts/meshes.md). Kan användas för att indexera `MeshComponent.UsedMaterials` och slå upp [materialet](../../concepts/materials.md) vid den tidpunkten.
* **HitPosition:** Utrymmes positionen där Ray korsar objektet.
* **HitNormal:** Ytans yta i mitten av nätet vid positionen för skärnings punkten.
* **DistanceToHit:** Avståndet från Ray-start positionen till träffen.

## <a name="next-steps"></a>Nästa steg

* [Objektgränser](../../concepts/object-bounds.md)
* [Åsidosätta hierarkiska tillstånd](override-hierarchical-state.md)
