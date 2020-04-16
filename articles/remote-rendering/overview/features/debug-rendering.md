---
title: Felsökningsrendering
description: Översikt över felsökningsrenderingar på serversidan
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: 675f8d988e64ed7b556f154f681ccb53ed1000c6
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394298"
---
# <a name="debug-rendering"></a>Felsökningsrendering

Felsökningsrenden API:et innehåller en rad globala alternativ för att ändra återgivning på serversidan med olika felsökningseffekter.

## <a name="available-debug-rendering-effects"></a>Tillgängliga felsökningsrenderingseffekter

|Inställning                          | Verkan                               |
|---------------------------------|:-------------------------------------|
|Ramräknare                    | Återger ett textöverlägg i ramens övre vänstra hörn. Texten visar det aktuella ram-ID:t på serversidan, som kontinuerligt ökas när renderingen fortsätter. |
|Antal polygoner                    | Återger ett textöverlägg i ramens övre vänstra hörn. Texten visar den återgivningsmängd som för närvarande återges, samma värde som efterfrågas av [prestandafrågor på serversidan](performance-queries.md)| 
|Trådram                        | Om det är aktiverat återges alla objektgeometri som läses in på servern i wireframe-läge. Endast kanterna på polygoner rastreras n detta läge. |

Följande kod möjliggör dessa felsökningseffekter:

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![Felsökningsrendering](./media/debug-rendering.png)

> [!NOTE]
> Alla felsökningsrenderingseffekter är globala inställningar som påverkar hela ramen.

## <a name="use-cases"></a>Användningsfall

Felsökningsrende API:et är avsett för enkla felsökningsuppgifter, som att verifiera att tjänstanslutningen faktiskt är igång korrekt. Textåtergivningsalternativen påverkar direkt de nedströms videobildrutorna. Om du aktiverar dem verifieras om nya bildrutor tas emot och videoavkodas korrekt.

De angivna effekterna ger dock inte någon detaljerad introspektion i tjänsten hälsa. [Prestandafrågor på serversidan](performance-queries.md) rekommenderas för det här användningsfallet.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

* Om du aktiverar textöverläggen uppstår inte mycket eller ingen prestanda.
* Dessutom aktivera overlay medför en icke-trivial prestanda overhead, men det kan variera beroende på scenen. För komplexa scener kan det här läget leda till att bildrutehastigheten sjunker under 60-Hz-målet.

## <a name="next-steps"></a>Nästa steg

* [Renderingsmodeller](../../concepts/rendering-modes.md)
* [Prestandafrågor på serversidan](performance-queries.md)
