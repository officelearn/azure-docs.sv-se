---
title: Enkelsidig rendering
description: Beskriver enkelsidiga renderingsinställningar och användningsfall
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682018"
---
# <a name="single-sided-rendering"></a>Enkelsidig rendering

De flesta renderare använder [back-face culling](https://en.wikipedia.org/wiki/Back-face_culling) för att förbättra prestanda. Men när maskor skärs upp med [skurna plan,](cut-planes.md)användare kommer ofta att titta på baksidan av trianglar. Om dessa trianglar gallras bort, ser resultatet inte övertygande.

Sättet att på ett tillförlitligt sätt förhindra detta problem, är att göra trianglar *dubbelsidiga*. Eftersom du inte använder utgallring i baksidan har prestandakonsekvenser, växlar Azure Remote Rendering som standard endast till dubbelsidig rendering för nät som korsar med ett utklippt plan.

Med inställningen *för enkelsidig återgivning* kan du anpassa det här beteendet.

> [!CAUTION]
> Den ensidiga renderingsinställningen är en experimentell funktion. Det kan tas bort igen i framtiden. Ändra inte standardinställningen, om det inte verkligen löser ett kritiskt problem i ditt program.

## <a name="prerequisites"></a>Krav

Den ensidiga återgivningsinställningen `opaqueMaterialDefaultSidedness` har bara en effekt för maskor `SingleSided`som har [konverterats](../../how-tos/conversion/configure-model-conversion.md) med alternativet inställt på . Som standard är det `DoubleSided`här alternativet inställt på .

## <a name="single-sided-rendering-setting"></a>Inställning för enkelsidig återgivning

Det finns tre olika lägen:

**Normalt:** I det här läget återges alltid maskor när de konverteras. Det innebär att maskor som konverterats med `opaqueMaterialDefaultSidedness` inställd på `SingleSided` alltid kommer att få återges med back-face culling aktiverat, även när de skär ett snitt plan.

**DynamicDoubleSiding:** I det här läget, när ett snittplan skär ett nät, växlas det automatiskt till dubbelsidig rendering. Det här läget är standardläget.

**AlwaysDoubleSided:** Tvingar alla enkelsidiga geometri att återges dubbelsidig hela tiden. Det här läget är mestadels exponerat så att du enkelt kan jämföra prestandapåverkan mellan enkelsidig och dubbelsidig rendering.

Ändra enkelsidiga renderingsinställningar kan göras på följande sätt:

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

* [Klipp plan](cut-planes.md)
* [Konfigurera modellkonverteringen](../../how-tos/conversion/configure-model-conversion.md)
