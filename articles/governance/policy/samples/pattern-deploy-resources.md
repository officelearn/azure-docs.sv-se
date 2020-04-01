---
title: 'Mönster: Distribuera resurser med en principdefinition'
description: Det här Azure-principmönstret är ett exempel på hur du distribuerar resurser med en principdefinition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172676"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure Princip-mönster: distribuera resurser

[Effekten deployIfNotExists](../concepts/effects.md#deployifnotexists) gör det möjligt att distribuera en [Azure Resource Manager-mall](../../../azure-resource-manager/templates/overview.md) när du skapar eller uppdaterar en resurs som inte är kompatibel. Den här metoden kan föredras framför att använda [neka](../concepts/effects.md#deny) effekten eftersom det låter resurser fortsätta att skapas, men säkerställer att ändringarna görs för att göra dem kompatibla.

## <a name="sample-policy-definition"></a>Exempel på principdefinition

Den här principdefinitionen använder `type` fältoperatorn för att utvärdera resursen som skapats eller uppdaterats. **field** När resursen är en _Microsoft.Network/virtualNetworks_söker principen efter en nätverksbevakare på den nya eller uppdaterade resursens plats. Om en matchande nätverksbevakare inte finns distribueras Resource Manager-mallen för att skapa den resurs som saknas.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Förklaring

#### <a name="existencecondition"></a>existensVillkor

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

**Properties.policyRule.then.details-blocket** talar om för Azure Policy vad du ska leta efter som är relaterade till den skapade eller uppdaterade resursen i **properties.policyRule.if-blocket.** I det här exemplet måste en nätverksbevakare i **field** `location` **resursgruppsnätverketWatcherRG** finnas med ett fält som är lika med platsen för den nya eller uppdaterade resursen. Med `field()` hjälp av funktionen kan **förekomstenKonditionen** komma åt egenskaper `location` för den nya eller uppdaterade resursen, särskilt egenskapen.

#### <a name="roledefinitionids"></a>rollDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

Egenskapen **roleDefinitionIds** _array_ i **properties.policyRule.then.details-blocket** anger principdefinitionen vilka rättigheter den hanterade identiteten behöver för att distribuera den inkluderade Resource Manager-mallen. Den här egenskapen måste anges så att den innehåller roller som har de behörigheter som krävs av malldistributionen, men bör använda begreppet "princip med lägsta behörighet" och bara ha nödvändiga åtgärder och inget mer.

#### <a name="deployment-template"></a>Distributionsmall

**Distributionsdelen** av principdefinitionen har ett **egenskapsblock** som definierar de tre kärnkomponenterna:

- **läge** - Den här egenskapen anger [distributionsläget](../../../azure-resource-manager/templates/deployment-modes.md) för mallen.

- **mall** - Den här egenskapen innehåller själva mallen. I det här exemplet anger **parametern platsmall** platsen för den nya nätverksbevakarresursen.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parametrar** - Den här egenskapen definierar parametrar som anges i **mallen**. Parameternamnen måste matcha vad som definieras i **mallen**. I det här exemplet heter parametern **plats** att matcha. Värdet **för lagerställe** `field()` använder funktionen igen för att hämta värdet för den utvärderade resursen, som är det virtuella nätverket i **policyRule.if-blocket.**

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).