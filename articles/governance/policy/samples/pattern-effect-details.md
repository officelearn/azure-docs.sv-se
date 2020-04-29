---
title: 'Mönster: påverkan av en princip definition'
description: Detta Azure Policy mönster ger exempel på hur du kan använda de olika effekterna av en princip definition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80372640"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy mönster: effekter

Azure Policy har ett antal [effekter](../concepts/effects.md) som avgör hur tjänsten reagerar på icke-kompatibla resurser. Vissa effekter är enkla och kräver inga ytterligare egenskaper i princip definitionen medan andra kräver flera egenskaper.

## <a name="sample-1-simple-effect"></a>Exempel 1: enkel påverkan

Den här princip definitionen kontrollerar om taggen som definierats i parametern **TagName** finns på den utvärderade resursen. Om taggen ännu inte finns utlöses [ändrings](../concepts/effects.md#modify) resultatet för att lägga till taggen med värdet i parametern **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Exempel 1: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

En **ändrings** funktion kräver **policyRule. then. information** -block som definierar **roleDefinitionIds** och **åtgärder**. Dessa parametrar informerar Azure Policy vilka roller som behövs för att lägga till taggen och åtgärda resursen och vilken **ändrings** åtgärd som ska utföras. I det här exemplet läggs **åtgärden** _till_ och parametrarna används för att ange taggen och dess värde.

## <a name="sample-2-complex-effect"></a>Exempel 2: komplex påverkan

Den här princip definitionen granskar varje virtuell dator för när ett tillägg, definierat i parametrarna **utgivare** och **typ**, inte finns. Den använder [auditIfNotExists](../concepts/effects.md#auditifnotexists) för att kontrol lera en resurs som är relaterad till den virtuella datorn för att se om det finns en instans som matchar de definierade parametrarna. I det här exemplet kontrol leras typen av **tillägg** .

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Exempel 2: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

En **auditIfNotExists** -påverkan kräver **policyRule. then. information** block för att definiera både en **typ** och den **existenceCondition** som ska sökas efter. **ExistenceCondition** använder princip språk element, till exempel [logiska operatorer](../concepts/definition-structure.md#logical-operators), för att avgöra om det finns en matchande relaterad resurs. I det här exemplet definieras värdena som kontrol leras mot varje [alias](../concepts/definition-structure.md#aliases) i parametrar.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).