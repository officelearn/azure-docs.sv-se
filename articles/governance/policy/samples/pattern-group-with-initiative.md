---
title: 'Mönster: Definitioner av grupppolicyer med initiativ'
description: Det här Azure-principmönstret är ett exempel på hur du grupperar principdefinitioner i ett initiativ
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172858"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure-principmönster: definitioner av grupprinciper

Ett initiativ är en grupp politiska definitioner. Genom att gruppera relaterade principdefinitioner i ett enda objekt kan du skapa en enskild tilldelning som skulle ha varit flera tilldelningar.

## <a name="sample-initiative-definition"></a>Definition av exempelinitiativ

Det här initiativet distribuerar två principdefinitioner, som var och en tar **parametrarna tagName** och **tagValue.** Själva initiativet har två parametrar: **costCenterValue** och **productNameValue**.
Dessa initiativparametrar tillhandahålls var och en av de grupperade principdefinitionerna. Den här designen maximerar återanvändningen av befintliga principdefinitioner samtidigt som antalet tilldelningar som skapats för att implementera dem begränsas efter behov.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Förklaring

#### <a name="initiative-parameters"></a>Initiativparametrar

Ett initiativ kan definiera egna parametrar som sedan skickas till de grupperade principdefinitionerna.
I det här exemplet definieras både **costCenterValue** och **productNameValue** som initiativparametrar. Värdena anges när initiativet tilldelas.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Innehåller principdefinitioner

Varje inkluderad principdefinition måste ange **principDefinitionId** och en **parametermatris** om principdefinitionen accepterar parametrar. I kodavsnittet nedan tar den medföljande principdefinitionen två parametrar: **tagName** och **tagValue**. **tagName** definieras med en litteral, men **tagValue** använder parametern **costCenterValue** som definieras av initiativet. Den här genomströmningen av värden förbättrar återanvändningen.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).