---
title: Rumsliga frågor
description: Hur man gör rumsliga frågor i en scen
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680536"
---
# <a name="spatial-queries"></a>Rumsliga frågor

Rumsliga frågor är åtgärder som du kan fråga fjärråtergivningstjänsten om vilka objekt som finns i ett område. Rumsliga frågor används ofta för att implementera interaktioner, till exempel att räkna ut vilket objekt en användare pekar på.

Alla rumsliga frågor utvärderas på servern. Följaktligen är de asynkrona åtgärder och resultaten kommer fram med en fördröjning som beror på nätverksfördröjningen. Eftersom varje rumslig fråga genererar nätverkstrafik, var noga med att inte göra för många på en gång.

## <a name="collision-meshes"></a>Kollisionsmaskor

Rumsliga frågor drivs av [Havok fysikmotor](https://www.havok.com/products/havok-physics) och kräver en särskild kollision mesh att vara närvarande. Som standard genererar [modellkonvertering](../../how-tos/conversion/model-conversion.md) kollisionsnät. Om du inte behöver rumsliga frågor på en komplex modell kan du inaktivera generering av kollisionsnät i [konverteringsalternativen,](../../how-tos/conversion/configure-model-conversion.md)eftersom det påverkar på flera sätt:

* [Modellkonverteringen](../../how-tos/conversion/model-conversion.md) kommer att ta betydligt längre tid.
* Konverterade modellfilstorlekar är märkbart större, vilket påverkar nedladdningshastigheten.
* Körningen är längre.
* Körning CPU-minne förbrukningen är högre.
* Det finns en liten körning prestanda overhead för varje modell instans.

## <a name="ray-casts"></a>Ray kastar

En *ray cast* är en rumslig fråga där körningen kontrollerar vilka objekt som korsas av en stråle, med början vid en viss position och pekar i en viss riktning. Som optimering ges också ett maximalt ray-avstånd, för att inte söka efter objekt som är för långt borta.

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

Det finns tre träffinsamlingslägen:

* **Närmaste:** I det här läget rapporteras endast närmaste träff.
* **Alla:** Föredrar detta läge när allt du vill veta är *om* en stråle skulle träffa något, men bryr sig inte vad som träffades exakt. Denna fråga kan vara betydligt billigare att utvärdera, men har också bara ett fåtal program.
* **Alla:** I detta läge rapporteras alla träffar längs strålen, sorterade efter avstånd. Använd inte det här läget om du inte verkligen behöver mer än den första träffen. Begränsa antalet rapporterade träffar `MaxHits` med alternativet.

Om du vill utesluta objekt som selektivt övervägs för ray-casts kan komponenten [HierarchicalStateOverrideComponent](override-hierarchical-state.md) användas.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Träffresultat

Resultatet av en ray cast-fråga är en rad träffar. Matrisen är tom om inget objekt träffades.

En träff har följande egenskaper:

* **HitEntity:** Vilken [enhet](../../concepts/entities.md) träffades.
* **SubPartId:** Vilken *undergrupp* träffades i en [MeshComponent](../../concepts/meshes.md). Kan användas för `MeshComponent.UsedMaterials` att indexera och leta upp [materialet](../../concepts/materials.md) vid den tidpunkten.
* **HitPosition:** Den världsrymdposition där strålen skär objektet.
* **HitNormal:** Världens rymdyta normal av nätet vid skärningspunktens position.
* **AvståndToHit:** Avståndet från strålens startläge till träffen.

## <a name="next-steps"></a>Nästa steg

* [Objekt bounds](../../concepts/object-bounds.md)
* [Åsidosätta hierarkiska tillstånd](override-hierarchical-state.md)
