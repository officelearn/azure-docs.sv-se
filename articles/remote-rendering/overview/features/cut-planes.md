---
title: Klippa ut planer
description: Förklarar vad klipp ut plan är och hur du använder dem
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681030"
---
# <a name="cut-planes"></a>Klippa ut planer

Ett *klipp plan* är en visuell funktion som klipper ut pixlar på ena sidan av ett virtuellt plan, och som visar insidan av [maskor](../../concepts/meshes.md).
Bilden nedan visar resultatet. Den vänstra delen visar det ursprungliga nätet, till höger kan se ut i nätet:

![Klipp ut plan](./media/cutplane-1.png)

## <a name="limitations"></a>Begränsningar

* För tillfället stöder Azure-fjärråter givning **högst åtta aktiva klipp plan**. Du kan skapa fler klipp komponenter, men om du försöker aktivera fler samtidigt kommer den att ignorera aktiveringen. Inaktivera andra plan först om du vill växla vilken komponent som ska påverka scenen.
* Varje styckat plan påverkar alla fjärråterställda objekt. Det finns för närvarande inget sätt att utesluta vissa objekt eller nät delar.
* Klipp ut plan är enbart en visuell funktion, de påverkar inte resultatet av [spatiala frågor](spatial-queries.md). Om du vill använda Ray i ett uppdelat nät kan du justera start punkten för Ray-ytan så att den är i det avskurna planet. På så sätt kan Ray bara trycka på synliga delar.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Varje aktivt klipp plan kostar en låg kostnad under åter givningen. Inaktivera eller ta bort klipp plan när de inte behövs.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Du lägger till ett klipp plan till scenen genom att skapa en *CutPlaneComponent*. Planens plats och orientering bestäms av komponentens ägar [enhet](../../concepts/entities.md).

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Egenskaper för CutPlaneComponent

Följande egenskaper visas i en klipp Plans komponent:

* **Aktive rad:** Du kan tillfälligt stänga av klipp ut plan genom att inaktivera komponenten. Inaktiverade klipp plan medför inte åter givning av kostnader och räknas inte mot den globala gränsen för klipp ut plan.

* **Normal:** Anger vilken riktning (+ X,-X, + Y,-Y, + Z,-Z) som används som normal för planet. Den här riktningen är relativ till ägar entitetens orientering. Flytta och rotera entiteten ägare för exakt placering.

* **FadeColor** och **FadeLength:**

  Om alpha-värdet för *FadeColor* är skilt från noll, kommer pixlarna nära det avskurna planet att tonas mot RGB-delen av FadeColor. Styrkan i alfa kanalen avgör om den ska tonas helt mot tonings färgen eller bara delvis. *FadeLength* definierar över vilket avstånd som den här toningen ska äga rum.

## <a name="next-steps"></a>Nästa steg

* [Enkels idig rendering](single-sided-rendering.md)
* [Rumsliga frågor](spatial-queries.md)
