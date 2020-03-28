---
title: 'Mönster: Logiska operatorer i en principdefinition'
description: Det här Azure-principmönstret innehåller exempel på hur du använder de logiska operatörerna i en principdefinition.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172851"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure-principmönster: logiska operatorer

En principdefinition kan innehålla flera villkorssatser. Du kan behöva varje uttalande för att vara sant eller bara behöver några av dem för att vara sant. För att stödja dessa behov har språket [logiska operatorer](../concepts/definition-structure.md#logical-operators) för **inte**, **allOf**och **anyOf**. De är valfria och kan kapslas för att skapa komplexa scenarier.

## <a name="sample-1-one-logical-operator"></a>Exempel 1: En logisk operator

Den här principdefinitionen utvärderar CosmosDB-konton för att se om automatiska redundanser och flera skrivplatser har konfigurerats. När de inte är det utlöser [granskningen](../concepts/effects.md#audit) en loggpost när den icke-kompatibla resursen skapas eller uppdateras.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Exempel 1: Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

Den **policyRule.if** blocket använder en enda **allOf** för att säkerställa att alla tre villkoren är sanna.
Endast när alla dessa villkor utvärderas till true utlöser **granskningseffekten.**

## <a name="sample-2-multiple-logical-operators"></a>Exempel 2: Flera logiska operatorer

Den här principdefinitionen utvärderar resurser för ett namngivningsmönster. Om en resurs inte matchar [nekas](../concepts/effects.md#deny)den .

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Exempel 2: Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Det här **policyRule.if-blocket** innehåller också en enda **allOf**, men varje villkor är insvept med den **inte** logiska operatorn. Villkoret inuti den **inte** logiska operatorn utvärderar först och utvärderar sedan **att inte** avgöra om hela satsen är sann eller falsk. Om båda **inte** logiska operatorer utvärderar till true utlöses principeffekten.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).