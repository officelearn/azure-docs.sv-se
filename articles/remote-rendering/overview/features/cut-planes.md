---
title: Klipp plan
description: Förklarar vad klippta plan är och hur man använder dem
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681030"
---
# <a name="cut-planes"></a>Klipp plan

Ett *snittplan* är en visuell funktion som klipper pixlar på ena sidan av ett virtuellt plan, vilket avslöjar insidan av [maskor](../../concepts/meshes.md).
Bilden nedan visar effekten. Den vänstra visar den ursprungliga nätet, till höger kan man titta inuti nätet:

![Klipp plan](./media/cutplane-1.png)

## <a name="limitations"></a>Begränsningar

* För närvarande stöder Azure Remote Rendering **högst åtta aktiva cut-plan**. Du kan skapa fler klippta plankomponenter, men om du försöker aktivera fler samtidigt ignorerar den aktiveringen. Inaktivera andra plan först om du vill byta vilken komponent som ska påverka scenen.
* Varje klippplan påverkar alla fjärrrenderade objekt. Det finns för närvarande inget sätt att utesluta specifika objekt eller maskdelar.
* Klipp plan är rent en visuell funktion, de påverkar inte resultatet av [rumsliga frågor](spatial-queries.md). Om du vill stråle kastas i en cut-open mesh, kan du justera startpunkten för strålen att vara på cut planet. På så sätt kan strålen bara träffa synliga delar.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Varje aktivt skärningsplan medför en liten kostnad under rendering. Inaktivera eller ta bort klippta plan när de inte behövs.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Du lägger till ett klippt plan till scenen genom att skapa en *CutPlaneComponent*. Planets placering och orientering bestäms av komponentens [ägarenhet](../../concepts/entities.md).

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

Följande egenskaper exponeras på en cut plane-komponent:

* **Aktiverad:** Du kan tillfälligt stänga av flygplan genom att inaktivera komponenten. Funktionshindrade klippplan ådrar sig inte rendering overhead och räknas inte heller mot den globala cut plane-gränsen.

* **Normalt:** Anger vilken riktning (+X,-X,+Y,-Y,+Z,-Z) används som planet normalt. Den här riktningen är relativ till ägarentitetens orientering. Flytta och rotera ägarentiteten för exakt placering.

* **FadeColor** och **FadeLength:**

  Om alfavärdet för *FadeColor* inte är noll tonas pixlar nära det utklippta planet mot RGB-delen av FadeColor. Alfakanalens styrka avgör om den kommer att blekna helt mot toningsfärgen eller bara delvis. *FadeLength* definierar över vilket avstånd denna toning kommer att äga rum.

## <a name="next-steps"></a>Nästa steg

* [Enkelsidig rendering](single-sided-rendering.md)
* [Rumsliga frågor](spatial-queries.md)
