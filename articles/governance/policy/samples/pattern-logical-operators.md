---
title: 'Mönster: logiska operatorer i en princip definition'
description: Det här Azure Policys mönstret innehåller exempel på hur du använder logiska operatorer i en princip definition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172851"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy mönster: logiska operatorer

En princip definition kan innehålla flera villkorliga uttryck. Du kan behöva varje instruktion för att vara sann eller behöver bara vissa av dem vara sanna. För att stödja dessa behov har språket [logiska operatörer](../concepts/definition-structure.md#logical-operators) för **not**, **allOf**och **anyOf**. De är valfria och kan kapslas för att skapa komplexa scenarier.

## <a name="sample-1-one-logical-operator"></a>Exempel 1: en logisk operator

Den här princip definitionen utvärderar CosmosDB-konton för att se om automatisk redundans och flera Skriv platser har kon figurer ATS. När de inte är det utlöses [granskningen](../concepts/effects.md#audit) och skapar en loggpost när den icke-kompatibla resursen skapas eller uppdateras.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Exempel 1: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**PolicyRule. om** block använder en enda **allOf** för att säkerställa att alla tre villkoren är sanna.
Endast när alla dessa villkor utvärderas till sant, utlöses **gransknings** resultatet.

## <a name="sample-2-multiple-logical-operators"></a>Exempel 2: flera logiska operatorer

Den här princip definitionen utvärderar resurser för ett namngivnings mönster. Om en resurs inte matchar är den [nekad](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Exempel 2: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Den här **policyRule. om** blocket även innehåller en enda **allOf**, men varje villkor omsluts med den **icke** -logiska operatorn. Villkoret i den **icke** -logiska operatorn utvärderas först och utvärderar **inte** för att avgöra om hela satsen är true eller false. Om båda **inte** är logiska operatorer som utvärderas som sant utlöses princip påverkan.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).