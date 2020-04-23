---
title: Rendering av felsökning
description: Översikt över felsökningsrenderingar på serversidan
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868163"
---
# <a name="debug-rendering"></a>Rendering av felsökning

Felsökningsrenden API:et innehåller en rad globala alternativ för att ändra återgivning på serversidan med olika felsökningseffekter.

## <a name="available-debug-rendering-effects"></a>Tillgängliga felsökningsrenderingseffekter

|Inställning                          | Verkan                               |
|---------------------------------|:-------------------------------------|
|Ramräknare                    | Återger ett textöverlägg i ramens övre vänstra hörn. Texten visar det aktuella ram-ID:t på serversidan, som kontinuerligt ökas när renderingen fortsätter. |
|Antal polygoner                    | Återger ett textöverlägg i ramens övre vänstra hörn. Texten visar den återgivningsmängd som för närvarande återges, samma värde som efterfrågas av [prestandafrågor på serversidan](performance-queries.md)| 
|Trådram                        | Om det är aktiverat återges alla objektgeometri som läses in på servern i wireframe-läge. Endast kanterna på polygoner rastreras i det här läget. |

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
* Aktivera wireframe-läget medför en icke-trivial prestanda overhead, men det kan variera beroende på scenen. För komplexa scener kan det här läget leda till att bildhastigheten sjunker under 60-Hz-målet.

## <a name="next-steps"></a>Nästa steg

* [Renderingsmodeller](../../concepts/rendering-modes.md)
* [Prestandafrågor på serversidan](performance-queries.md)
