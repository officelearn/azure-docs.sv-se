---
title: 'Mönster: fält egenskaper i en princip definition'
description: Detta Azure Policy mönster innehåller ett exempel på hur du använder fält egenskaper i en princip definition.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 87c550e5fb3ef0efbf219efc738a69071d2e89ba
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "85565724"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy mönster: fält egenskaper

[Fält](../concepts/definition-structure.md#fields) operatorn utvärderar den angivna egenskapen eller [aliaset](../concepts/definition-structure.md#aliases) till ett angivet värde för ett visst [villkor](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Exempel på princip definition

Med den här princip definitionen kan du definiera tillåtna regioner som uppfyller organisationens krav på geografisk plats. De tillåtna resurserna definieras i parametern **listOfAllowedLocations** (_matris_). Resurser som matchar definitionen [nekas](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

**Fält** operatorn används tre gånger inom den [logiska operatorn](../concepts/definition-structure.md#logical-operators) **allOf**.

- Den första användningen utvärderar `location` egenskapen med **notIn** -villkoret till parametern **listOfAllowedLocations** . **notIn** fungerar som förväntat en _matris_ och parametern är en _matris_. Om den `location` nya eller uppdaterade resursen inte finns i listan över godkända objekt, utvärderas det här elementet som sant.
- Den andra användningen utvärderar också `location` egenskapen, men använder **notEquals** -villkoret för att se om resursen är _Global_. Om den `location` skapade eller uppdaterade resursen inte är _Global_, utvärderas elementet som sant.
- Den senaste användningen utvärderar `type` egenskapen och använder **notEquals** -villkoret för att verifiera resurs typen är inte _Microsoft. AzureActiveDirectory/b2cDirectories_. Om den inte är det, utvärderas elementet som sant.

Om alla tre villkors satserna i den logiska operatorn **allOf** utvärderar True, blockeras skapandet eller uppdateringen av Azure policy.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).