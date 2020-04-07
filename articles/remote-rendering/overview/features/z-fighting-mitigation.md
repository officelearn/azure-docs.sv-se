---
title: Z-fighting lindring
description: Beskriver tekniker för att minska z-fighting artefakter
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680458"
---
# <a name="z-fighting-mitigation"></a>Z-fighting lindring

När två ytor överlappar varandra är det oklart vilken som ska återges ovanpå den andra. Resultatet varierar även per pixel, vilket resulterar i vyberoende artefakter. Följaktligen, när kameran eller nätet rör sig, dessa mönster flimrar märkbart. Denna artefakt kallas *z-fighting*. För AR- och VR-applikationer intensifieras problemet eftersom huvudmonterade enheter naturligt alltid rör sig. För att förhindra att tittaren obehag z-fighting begränsning funktionalitet är tillgänglig i Azure Remote Rendering.

## <a name="z-fighting-mitigation-modes"></a>Z-fighting begränsningslägen

|Situationen                        | Resultat                               |
|---------------------------------|:-------------------------------------|
|Regelbundna z-fighting               |![Z-slåss](./media/zfighting-0.png)|
|Z-fighting mitigation aktiverat    |![Z-slåss](./media/zfighting-1.png)|
|Markering av schackbräde aktiverat|![Z-slåss](./media/zfighting-2.png)|

Följande kod möjliggör z-fighting mitigation:

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> Z-fighting mitigation är en global inställning som påverkar alla renderade maskor.

## <a name="reasons-for-z-fighting"></a>Orsaker till z-fighting

Z-fighting händer främst av två skäl:

1. när ytorna är mycket långt borta från kameran försämras precisionen i deras djupvärden och värdena blir omöjliga att skilja
1. när ytor i ett nät överlappar varandra fysiskt

Det första problemet kan alltid hända och är svårt att eliminera. Om detta händer i din ansökan, se till att förhållandet mellan den *nära planet* avstånd till den *bortre planet* avståndet är så lågt som praktiskt. Till exempel, ett nära plan på avstånd 0,01 och långt plan på avstånd 1000 kommer att skapa detta problem mycket tidigare, än att ha nära planet på 0,1 och långt plan på avstånd 20.

Det andra problemet är en indikator för dåligt författat innehåll. I den verkliga världen kan två objekt inte vara på samma plats samtidigt. Beroende på programmet kanske användarna vill veta om överlappande ytor finns och var de befinner sig. Till exempel bör en CAD-scen av en byggnad som ligger till grund för en verklig värld konstruktion, inte innehålla fysiskt omöjliga ytkorsningar. För att möjliggöra visuell inspektion är markeringsläget tillgängligt, som visar potentiella z-fighting som ett animerat schackbrädemönster.

## <a name="limitations"></a>Begränsningar

Den tillförda z-fighting lindring är en bästa insats. Det finns ingen garanti för att det tar bort alla z-fighting. Också kommer det automatiskt att föredra en yta över en annan. Således när du har ytor som är för nära varandra, kan det hända att "fel" yta hamnar på toppen. Ett vanligt problemär när text och andra dekaler appliceras på en yta. Med z-fighting lindring aktiverat dessa detaljer kan lätt bara försvinna.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

* Att möjliggöra z-fighting lindring medför liten eller ingen prestanda overhead.
* Dessutom gör det möjligt för z-fighting overlay medför en icke-trivial prestanda overhead, men det kan variera beroende på scenen.

## <a name="next-steps"></a>Nästa steg

* [Återgivningslägen](../../concepts/rendering-modes.md)
* [Sent skede omprojection](late-stage-reprojection.md)
