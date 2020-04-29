---
title: Enkelsidig rendering
description: Beskriver inställningar och användnings fall för Enkels idiga åter givning
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682018"
---
# <a name="single-sided-rendering"></a>Enkelsidig rendering

De flesta åter givnings programmen använder [backend-culling](https://en.wikipedia.org/wiki/Back-face_culling) för att förbättra prestandan. När maskorna klipps av öppen med [Klipp ut plan](cut-planes.md)kommer användarna ofta att titta på bak sidan av trianglar. Om dessa trianglar är slaktade, ser inte resultatet ut som övertygande.

Sättet att förhindra det här problemet på ett tillförlitligt sätt är att rendera trianglar *dubbels idiga*. Eftersom det inte går att använda backend-culling har prestanda effekter, som standard växlar Azure-fjärrrendering endast till dubbels idig åter givning för maskor som skärs med ett klipp plan.

Med inställningen med *Enkels idig åter givning* kan du anpassa det här beteendet.

> [!CAUTION]
> Inställningen för Enkels idig rendering är en experimentell funktion. Den kan tas bort igen i framtiden. Ändra inte standardinställningen, om det inte löser ett kritiskt problem i ditt program.

## <a name="prerequisites"></a>Krav

Inställningen med enkel sida åter givning har bara en effekt för maskor som har [konverterats](../../how-tos/conversion/configure-model-conversion.md) med `opaqueMaterialDefaultSidedness` alternativet inställt på `SingleSided`. Som standard är det här alternativet inställt på `DoubleSided`.

## <a name="single-sided-rendering-setting"></a>Inställning med Enkels idig åter givning

Det finns tre olika lägen:

**Normal:** I det här läget återges alltid maskor när de konverteras. Det innebär att nät som konverteras `opaqueMaterialDefaultSidedness` med `SingleSided` set till alltid återges med en bakgrunds Cull aktive rad, även om de överlappar ett klipp plan.

**DynamicDoubleSiding:** I det här läget, när ett klipp plan överlappar ett nät, växlas det automatiskt till dubbels idig åter givning. Det här läget är standard läget.

**AlwaysDoubleSided:** Tvingar all Enkels idig geometri att renderas dubbels idiga hela tiden. Det här läget exponeras främst så att du enkelt kan jämföra prestanda påverkan mellan enkel sida och dubbels idig åter givning.

Du kan ändra inställningarna för enkel åter givning på följande sätt:

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>Nästa steg

* [Klippa ut planer](cut-planes.md)
* [Konfigurera modell konverteringen](../../how-tos/conversion/configure-model-conversion.md)
