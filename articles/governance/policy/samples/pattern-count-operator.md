---
title: 'Mönster: Räknaroperatorn i en principdefinition'
description: Det här Azure-principmönstret är ett exempel på hur du använder räknar-operatorn i en principdefinition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172949"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy-mönster: räknar-operatorn

[Operatorn count](../concepts/definition-structure.md#count) utvärderar \[ \* \] medlemmar i ett alias.

## <a name="sample-policy-definition"></a>Exempel på principdefinition

Den här [principdefinitionen granskar nätverkssäkerhetsgrupper](../concepts/effects.md#audit) som konfigurerats för att tillåta inkommande fjärrskrivbordsprotokoll (RDP) trafik.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Förklaring

Kärnkomponenterna i **räkneoperatorn** är _fält_, _var_och villkoret. Var och en markeras i kodavsnittet nedan.

- _anger_ antal som [alias](../concepts/definition-structure.md#aliases) att utvärdera medlemmar i. Här tittar vi på **securityRules\[ \* ** alias _array_ av nätverket säkerhetsgrupp.
- _där_ använder principspråket för att definiera vilka _matrismedlemmar_ som uppfyller villkoren. I det här exemplet grupperar en **allOf** logisk operator tre olika villkorsutvärderingar av _aliasmatrisegenskaper:_ _riktning,_ _åtkomst_och _destinationPortRange_.
- Räknevillkoret i det här exemplet är **större**. Antal utvärderas som sant när en eller flera medlemmar i _aliasmatrisen_ matchar where-satsen. _where_

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).