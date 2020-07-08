---
title: Konturrendering
description: Förklarar hur du gör markerings kon tur åter givning
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4f3889a0ba121cb9a3167c1f6ac95f0bed280539
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83759021"
---
# <a name="outline-rendering"></a>Konturrendering

De valda objekten kan markeras visuellt genom att lägga till dispositions åter givning via [komponenten för åsidosättning av hierarkiskt tillstånd](../../overview/features/override-hierarchical-state.md). I det här kapitlet beskrivs hur globala parametrar för dispositions åter givning ändras via klient-API: et.

Dispositions egenskaper är en global inställning. Alla objekt som använder dispositions åter givning använder samma inställning – det går inte att använda en linje färg per objekt.

## <a name="parameters-for-outlinesettings"></a>Parametrar för`OutlineSettings`

Klassen `OutlineSettings` innehåller inställningar som rör globala dispositions egenskaper. Den exponerar följande medlemmar:

| Parameter      | Typ    | Beskrivning                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Färgen som används för att rita konturen. Alfa delen ignoreras.         |
| `PulseRateHz`    | float   | Den hastighet med vilken kon tur variationer per sekund|
| `PulseIntensity` | float   | Intensiteten för kon tur pulsen. Måste vara mellan 0,0 för ingen pulsning och 1,0 för hel pulsning. Intensitet anger den minsta opaciteten för konturen som `MinOpacity = 1.0 - PulseIntensity` . |

![Beskriver ](./media/outlines.png) effekterna av att ändra `color` parametern från gult (vänster) till Magenta (mitt) och `pulseIntensity` från 0 till 0,8 (höger).

## <a name="example"></a>Exempel

Följande kod visar ett exempel på hur du ställer in dispositions parametrar via API: et:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = *session->Actions()->OutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->Color(outlineColor);
    outlineSettings->PulseRateHz(2.0f);
    outlineSettings->PulseIntensity(0.5f);
}
```

## <a name="performance"></a>Prestanda

Kon tur åter givning kan ha en betydande inverkan på åter givnings prestanda. Den här effekten varierar beroende på förhållandet mellan valda och icke-valda objekt för en viss ram.

## <a name="next-steps"></a>Nästa steg

* [Åsidosättande komponent för hierarkiskt tillstånd](../../overview/features/override-hierarchical-state.md)
