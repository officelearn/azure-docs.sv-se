---
title: Minskning av Z-fighting
description: Beskriver tekniker för att minimera z-bekämpnings artefakter
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 017bda3f29f70448504650965a0f19bbaf5852bd
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892719"
---
# <a name="z-fighting-mitigation"></a>Minskning av Z-fighting

När två ytor överlappar varandra är det inte uppenbart vilken som ska återges ovanpå den andra. Resultatet varierar även per bild punkt, vilket resulterar i kameravy-beroende artefakter. Det innebär att när kameran eller nätet flyttas flimmer dessa mönster märkbart. Den här artefakten kallas för *z-bekämpning*. För AR-och VR-program är problemet intensifierat eftersom de head-monterade enheterna naturligt alltid flyttar. För att förhindra att visnings programmet för visnings dämpande z-bekämpning är tillgängligt i Azure Remote rendering.

## <a name="z-fighting-mitigation-modes"></a>Minsknings lägen för Z-bekämpning

|Tillståndet                        | Resultat                               |
|---------------------------------|:-------------------------------------|
|Vanlig z-bekämpning               |![Z-bekämpning](./media/zfighting-0.png)|
|Minskning av Z-bekämpning aktive rad    |![Z-bekämpning](./media/zfighting-1.png)|
|Schack rutigt markering aktive rad|![Z-bekämpning](./media/zfighting-2.png)|

Följande kod aktiverar minskning av z-bekämpning:

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Actions()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```


> [!NOTE]
> Minskning av Z-bekämpning är en global inställning som påverkar alla återgivna maskor.

## <a name="reasons-for-z-fighting"></a>Orsaker till z-bekämpning

Z-bekämpning sker huvudsakligen av två anledningar:

1. När ytorna är mycket bort från kameran blir precisionen för deras djup värden degraderade och värdena inte särskiljbar
1. När ytor i ett nät fysiskt överlappar

Det första problemet kan alltid inträffa och är svårt att eliminera. Om detta händer i ditt program, se till att förhållandet mellan det *nära planet* avståndet till det *långt planet* avståndet är så litet som möjligt. Ett nära planet på avstånd 0,01 och långt plan på avstånd 1000 kommer att skapa det här problemet mycket tidigare, än att ha det nära planet vid 0,1 och det långt planet på avstånd 20.

Det andra problemet är en indikator för dåligt skapat innehåll. I den verkliga världen kan två objekt inte vara på samma plats samtidigt. Beroende på programmet kanske användarna vill veta om överlappande ytor finns och var de är. Till exempel är en CAD-scen för en byggnad som utgör grunden för en verklig världs konstruktion, bör inte innehålla något fysiskt möjligt funktions områdes skärnings punkt. För att möjliggöra visuell granskning är markerings läget tillgängligt, vilket visar potentiella z-skydd som ett animerat schack mönster.

## <a name="limitations"></a>Begränsningar

Den angivna minskningen av z-bekämpning är en bra ansträngning. Det finns ingen garanti för att den tar bort all z-bekämpning. Det innebär också att den automatiskt föredrar en yta över en annan. När du har ytor som är för nära varandra kan det hända att ytan "fel" hamnar överst. Ett vanligt problem är när text och andra decals tillämpas på en yta. Med z-bekämpning kan du använda den här informationen för att enkelt undvikas.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

* Att aktivera minskning av z-bekämpning innebär lite till ingen prestanda omkostnader.
* Även om du aktiverar ett överlägg med z-skydd uppstår en icke-trivial prestanda, men det kan variera beroende på scenen.

## <a name="next-steps"></a>Nästa steg

* [Renderingsmodeller](../../concepts/rendering-modes.md)
* [Omprojektion av sena steg](late-stage-reprojection.md)
