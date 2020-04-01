---
title: 'Mönster: Parametrar i en principdefinition'
description: Det här Azure-principmönstret är ett exempel på hur du använder parametrar i en principdefinition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172809"
---
# <a name="azure-policy-pattern-parameters"></a>Azure-principmönster: parametrar

En principdefinition kan göras dynamisk för att minska antalet principdefinitioner som behövs med hjälp av [parametrar](../concepts/definition-structure.md#parameters). Parametern definieras under principtilldelning. Parametrar har en uppsättning fördefinierade egenskaper som beskriver parametern och hur den används.

## <a name="sample-1-string-parameters"></a>Exempel 1: Strängparametrar

Den här principdefinitionen använder två parametrar, **tagName** och **tagValue** för att ange vad principtilldelningen söker efter på resurser. Med det här formatet kan principen användas för valfritt antal kombinationer av taggnamn och taggvärde, men endast behålla en enda principdefinition.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Exempel 1: Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

I den här delen av principdefinitionen definieras **parametern tagName** som en _sträng_ och en beskrivning tillhandahålls för dess användning.

Parametern används sedan i **policyRule.if-blocket** för att göra principen dynamisk. Här används det för att definiera det fält som utvärderas, vilket är en tagg med värdet **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Exempel 2: Matrisparametrar

Den här principdefinitionen använder en enda parameter, **listOfBandwidthinMbps,** för att kontrollera om expressränskringsresursen har konfigurerat bandbreddsinställningen till ett av de godkända värdena. Om den inte stämmer överens [nekas](../concepts/effects.md#deny)skapandet eller uppdateringen av resursen .

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Exempel 2: Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

I den här delen av principdefinitionen definieras parametern **listOfBandwidthinMbps** som en _matris_ och en beskrivning tillhandahålls för dess användning. Som _en matris_har den flera värden att matcha.

Parametern används sedan i **policyRule.if-blocket.** Som _en matrisparameter_ måste ett _matrisvillkor_
[condition](../concepts/definition-structure.md#conditions) **i** eller **inteIn** användas.
Här används det mot **alias serviceProvider.bandwidthInMbps** som ett av de definierade värdena.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).