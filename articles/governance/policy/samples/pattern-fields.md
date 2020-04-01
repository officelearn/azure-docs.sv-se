---
title: 'Mönster: Fältegenskaper i en principdefinition'
description: Det här Azure-principmönstret är ett exempel på hur du använder fältegenskaper i en principdefinition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172865"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure-principmönster: fältegenskaper

[Fältoperatorn](../concepts/definition-structure.md#fields) utvärderar den angivna egenskapen eller [aliaset](../concepts/definition-structure.md#aliases) till ett givet värde för ett givet [villkor](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Exempel på principdefinition

Med den här principdefinitionen kan du definiera tillåtna regioner som uppfyller organisationens geo-platskrav. De tillåtna resurserna definieras i **parameterlistanOfAllowedLocations** (_array_). Resurser som matchar definitionen [nekas](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

**Fältoperatorn** används tre gånger inom den [logiska operatorn](../concepts/definition-structure.md#logical-operators) **allOf**.

- Den första användningen `location` utvärderar egenskapen med **villkoret notIn** till parametern **listOfAllowedLocations.** **notIn** fungerar som den förväntar sig en _matris_ och parametern är en _matris_. Om `location` den skapade eller uppdaterade resursen inte finns i den godkända listan utvärderas det här elementet till true.
- Den andra användningen utvärderar också egenskapen, `location` men använder villkoret **notEquals** för att se om resursen är _global_. Om `location` den skapade eller uppdaterade resursen inte är _global_utvärderas det här elementet till true.
- Den senaste användningen `type` utvärderar egenskapen och använder villkoret **notEquals** för att validera resurstypen är inte _Microsoft.AzureActiveDirectory/b2cDirectories_. Om den inte är det utvärderas det här elementet till true.

Om alla tre villkorssatser i **allOf** logisk operator utvärderar sant, blockeras resursskapandet eller uppdateringen av Azure Policy.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).