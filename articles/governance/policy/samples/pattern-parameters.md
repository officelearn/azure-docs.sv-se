---
title: 'Mönster: parametrar i en princip definition'
description: Det här Azure Policys mönstret ger ett exempel på hur du använder parametrar i en princip definition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172809"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy mönster: parametrar

En princip definition kan göras dynamisk för att minska antalet princip definitioner som behövs med hjälp av [parametrar](../concepts/definition-structure.md#parameters). Parametern definieras under princip tilldelningen. Parametrar har en uppsättning fördefinierade egenskaper som beskriver parametern och hur den används.

## <a name="sample-1-string-parameters"></a>Exempel 1: sträng parametrar

Den här princip definitionen använder två parametrar, **TagName** och **tagValue** för att ange vad princip tilldelningen söker efter på resurser. Med det här formatet kan principen användas för valfritt antal kombinationer av taggnamn och kombinationer av taggar, men endast en enda princip definition.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Exempel 1: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

I den här delen av princip definitionen definieras **TagName** -parametern som en _sträng_ och en beskrivning anges för dess användning.

Parametern används sedan i **policyRule. om** blockeras för att göra principen dynamisk. Här används den för att definiera det fält som utvärderas, vilket är en tagg med värdet för **TagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Exempel 2: mat ris parametrar

Den här princip definitionen använder en enda parameter, **listOfBandwidthinMbps**, för att kontrol lera om ExpressRoute-kretsen har konfigurerat bandbredds inställningen till något av de godkända värdena. Om den inte matchar, [nekas](../concepts/effects.md#deny)skapandet eller uppdateringen av resursen.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Exempel 2: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

I den här delen av princip definitionen definieras parametern **listOfBandwidthinMbps** som en _matris_ och en beskrivning anges för dess användning. Som en _matris_har den flera värden att matcha.

Parametern används sedan i **policyRule. if** -block. Som en _mat ris_ parameter måste ett _mat ris_
[villkor](../concepts/definition-structure.md#conditions) **i** eller **notIn** användas.
Här används den mot **ServiceProvider. bandwidthInMbps** -aliaset som ett av de definierade värdena.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).