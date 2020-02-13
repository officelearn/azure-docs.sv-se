---
title: 'Mönster: operatorn Count i en princip definition'
description: Detta Azure Policy mönster ger ett exempel på hur du använder operatorn Count i en princip definition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172949"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy mönster: operatorn Count

Operatorn [Count](../concepts/definition-structure.md#count) utvärderar medlemmar i ett \[\*\] alias.

## <a name="sample-policy-definition"></a>Exempel på princip definition

Den här princip definitionen [granskar](../concepts/effects.md#audit) nätverks säkerhets grupper som kon figurer ATS för att tillåta inkommande Remote Desktop Protocol (RDP)-trafik.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Förklaring

Kärn komponenterna i operatorn **Count** är _Field_, _WHERE_och condition. Varje är markerat i kodfragmentet nedan.

- _fältet_ visar antalet [alias](../concepts/definition-structure.md#aliases) för att utvärdera medlemmar i. Här tittar vi på **securityRules-\[\*\]** Ali Aset _array_ för nätverks säkerhets gruppen.
- _där_ använder princip språket för att definiera vilka _mat ris_ medlemmar som uppfyller villkoren. I det här exemplet grupper en logisk **allOf** i tre olika villkor för alias för _mat ris_ egenskaper: _Direction_, _Access_och _destinationPortRange_.
- Count-villkoret i det här exemplet är **större**. Count utvärderas som sant när en eller flera medlemmar _i Ali Aset_ matchar _WHERE_ -satsen.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).