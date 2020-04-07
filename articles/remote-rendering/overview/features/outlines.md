---
title: Dispositionsrendering
description: Förklarar hur du gör markeringsdispositionsåtergivning
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680835"
---
# <a name="outline-rendering"></a>Dispositionsrendering

Markerade objekt kan markeras visuellt genom att lägga till dispositionsrendering via [komponenten Hierarkisk tillståndsöversidosättning](../../overview/features/override-hierarchical-state.md). I det här kapitlet beskrivs hur globala parametrar för dispositionsrendering ändras via klient-API:et.

Dispositionsegenskaper är en global inställning. Alla objekt som använder konturåtergivning använder samma inställning - det går inte att använda en konturfärg per objekt.

## <a name="parameters-for-outlinesettings"></a>Parametrar för`OutlineSettings`

Klassen `OutlineSettings` innehåller inställningarna som är relaterade till globala dispositionsegenskaper. Det exponerar följande medlemmar:

| Parameter      | Typ    | Beskrivning                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Färg4Ub | Den färg som används för att rita dispositionen. Alfadelen ignoreras.         |
| `PulseRateHz`    | float   | Den hastighet med vilken konturen svänger per sekund|
| `PulseIntensity` | float   | Intensiteten i konturpulseffekten. Måste vara mellan 0,0 för ingen pulserande och 1,0 för full pulserande. Intensitet anger implicit den minsta opaciteten `MinOpacity = 1.0 - PulseIntensity`för dispositionen som . |

![Konturer](./media/outlines.png) Effekten av `color` att ändra parametern från gult (vänster) till magenta (mitten) och `pulseIntensity` från 0 till 0,8 (höger).

## <a name="example"></a>Exempel

Följande kod visar ett exempel för att ange dispositionsparametrar via API:et:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Prestanda

Dispositionsrendering kan ha en betydande inverkan på renderingsprestanda. Den här effekten varierar beroende på rumslig relation mellan markerade och icke-markerade objekt för en viss ram.

## <a name="next-steps"></a>Nästa steg

* [Åsidosättkomponent för hierarkiskt tillstånd](../../overview/features/override-hierarchical-state.md)
