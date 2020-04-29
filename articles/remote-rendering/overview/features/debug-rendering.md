---
title: Rendering av felsökning
description: Översikt över åter givnings effekter på Server Sidan
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868163"
---
# <a name="debug-rendering"></a>Rendering av felsökning

API för fel söknings åter givning innehåller en uppsättning globala alternativ för att ändra åter givning på Server sidan med olika fel söknings effekter.

## <a name="available-debug-rendering-effects"></a>Tillgängliga fel söknings åter givnings effekter

|Inställningen                          | Verkan                               |
|---------------------------------|:-------------------------------------|
|Bild Rute räknare                    | Återger ett text överlägg i det övre vänstra hörnet av ramen. Texten visar det aktuella ram-ID: t för Server sidan, som kontinuerligt ökar när åter givningen fortsätter. |
|Antal polygoner                    | Återger ett text överlägg i det övre vänstra hörnet av ramen. Texten visar den aktuella åter givnings mängden av polygoner, samma värde som efter frågas av [prestanda frågor på Server sidan](performance-queries.md)| 
|Tråd block                        | Om aktive rad kommer all objekt geometri som läses in på servern att återges i tråd Rams läge. Endast kanterna på polygoner rastreras i det här läget. |

Följande kod aktiverar dessa fel söknings effekter:

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

![Fel söknings åter givning](./media/debug-rendering.png)

> [!NOTE]
> Alla effekter för fel söknings åter givning är globala inställningar som påverkar hela ramen.

## <a name="use-cases"></a>Användningsfall

API för fel söknings åter givning är avsett för enkla fel söknings åtgärder, som att verifiera att tjänst anslutningen faktiskt är igång och körs korrekt. Alternativen för text åter givning påverkar de nedströms video bild rutorna direkt. Om du aktiverar dem verifieras om nya ramar tas emot och video-avkodas korrekt.

De tillhandahållna effekterna ger dock ingen detaljerad introspektionsfunktionerna till tjänstens hälsa. [Prestanda frågorna på Server sidan](performance-queries.md) rekommenderas för det här användnings fallet.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

* Att aktivera text överlägg innebär lite till ingen prestanda.
* Aktivering av tråd Rams läge medför en icke-trivial prestanda, men det kan variera beroende på scenen. För komplexa scener kan det här läget orsaka att bild frekvensen släpps under 60-Hz-målet.

## <a name="next-steps"></a>Nästa steg

* [Renderingsmodeller](../../concepts/rendering-modes.md)
* [Prestandafrågor på serversidan](performance-queries.md)
