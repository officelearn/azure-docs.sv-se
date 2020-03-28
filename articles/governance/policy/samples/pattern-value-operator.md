---
title: 'Mönster: Värdeoperatorn i en principdefinition'
description: Det här Azure-principmönstret är ett exempel på hur du använder värdeoperatorn i en principdefinition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172788"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy-mönster: värdeoperatören

[Värdeoperatorn](../concepts/definition-structure.md#value) utvärderar [parametrar,](../concepts/definition-structure.md#parameters) [mallfunktioner](../concepts/definition-structure.md#policy-functions)som stöds eller litteraler till ett givet värde för ett givet [villkor](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Om resultatet av en _mallfunktion_ är ett fel misslyckas principutvärderingen. En misslyckad utvärdering är en implicit **neka**. Mer information finns i [undvika mallfel](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Exempel på principdefinition

Den här principdefinitionen lägger till eller ersätter taggen som anges i **parametertaggenName** (_sträng_) på resurser och ärver värdet för **tagName** från resursgruppen som resursen finns i. Den här utvärderingen sker när resursen skapas eller uppdateras. Som en [ändringseffekt](../concepts/effects.md#modify) kan reparationen köras på befintliga resurser via en [reparationsaktivitet](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

**Värdeoperatorn** används i **principRule.if-blocket** inom **egenskaper**. I det här exemplet används den [logiska operatorn](../concepts/definition-structure.md#logical-operators) **allOf** för att ange att båda villkorssatserna måste vara sanna för att effekten ska **ändras,** ska äga rum.

**värdet** utvärderar resultatet av mallfunktionen [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) till **villkoret notEquals** för ett tomt värde. Om taggnamnet som anges i **tagName** för den överordnade resursgruppen finns utvärderas villkorsvillkoret till true.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).