---
title: 'Mönster: Effekter av en principdefinition'
description: Det här Azure-principmönstret ger exempel på hur du använder de olika effekterna av en principdefinition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372640"
---
# <a name="azure-policy-pattern-effects"></a>Azure-principmönster: effekter

Azure Policy har ett antal [effekter](../concepts/effects.md) som avgör hur tjänsten reagerar på icke-kompatibla resurser. Vissa effekter är enkla och kräver inga ytterligare egenskaper i principdefinitionen medan andra kräver flera egenskaper.

## <a name="sample-1-simple-effect"></a>Prov 1: Enkel effekt

Den här principdefinitionen kontrollerar om taggen som definieras i **parametertaggenName** finns på den utvärderade resursen. Om taggen ännu inte finns [modify](../concepts/effects.md#modify) utlöses ändringseffekten för att lägga till taggen med värdet i **parametertaggenValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Exempel 1: Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

En **ändringseffekt** kräver **det principRule.then.details-block** som definierar **roleDefinitionIds** och **-åtgärder**. Dessa parametrar informerar Azure Policy vilka roller som behövs för att lägga till taggen och åtgärda resursen och vilken **ändringsåtgärd** som ska utföras. I det här exemplet läggs **åtgärden** _till_ och parametrarna används för att ange taggen och dess värde.

## <a name="sample-2-complex-effect"></a>Exempel 2: Komplex effekt

Den här principdefinitionen granskar varje virtuell dator för när ett tillägg, definierat i parametrar **utgivare** och **typ,** inte finns. Den använder [auditIfNotExists](../concepts/effects.md#auditifnotexists) för att kontrollera en resurs som är relaterad till den virtuella datorn för att se om det finns en instans som matchar de definierade parametrarna. I det här exemplet **kontrolleras tilläggstypen.**

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Exempel 2: Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

En **auditIfNotExists-effekt** kräver **att policyRule.then.details-blocket** definierar både en **typ** och **existensVillkoret** att leta efter. **FörekomstenVillkor** använder principspråkelement, till exempel [logiska operatorer,](../concepts/definition-structure.md#logical-operators)för att avgöra om det finns en matchande relaterad resurs. I det här exemplet definieras de värden som kontrolleras mot varje [alias](../concepts/definition-structure.md#aliases) i parametrar.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).