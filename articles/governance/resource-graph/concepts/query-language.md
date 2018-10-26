---
title: Förstå Azure Resource Graph-frågespråk
description: Beskriver hur frågespråk för Azure Resource Graph fungerar.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 09bcedc5250755f06ba23b84a0ae90b4d43a23db
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086173"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Förstå Azure Resource Graph-frågespråk

Frågespråket i Azure Resource Graph stöder ett antal operatorer och funktioner. Varje fungerar och driva utifrån [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md).

Det bästa sättet att lära dig frågespråket som används av resursen Graph är att börja med dokumentation för Azure Data Explorer [frågespråk](/azure/kusto/query/index). Det ger en förståelse om hur språket är strukturerad och hur de olika stöds operatorer och funktioner fungerar tillsammans.

## <a name="supported-tabular-operators"></a>Tabular operatorer som stöds

Här är listan över tabular operatorer som stöds i resursen Graph:

- [antal](/azure/kusto/query/countoperator)
- [Distinkta](/azure/kusto/query/distinctoperator)
- [Utöka](/azure/kusto/query/extendoperator)
- [Gränsen](/azure/kusto/query/limitoperator)
- [order by-](/azure/kusto/query/orderoperator)
- [Projekt](/azure/kusto/query/projectoperator)
- [projekt-away](/azure/kusto/query/projectawayoperator)
- [Exemplet](/azure/kusto/query/sampleoperator)
- [exempel-distinkta](/azure/kusto/query/sampledistinctoperator)
- [Sortera efter](/azure/kusto/query/sortoperator)
- [Sammanfatta](/azure/kusto/query/summarizeoperator)
- [ta](/azure/kusto/query/takeoperator)
- [längst upp](/azure/kusto/query/topoperator)
- [TOP-nested](/azure/kusto/query/topnestedoperator)
- [TOP-hitters](/azure/kusto/query/tophittersoperator)
- [där](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funktioner som stöds

Här är listan över funktioner som stöds i resursen Graph:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [toString)](/azure/kusto/query/tostringfunction)

## <a name="next-steps"></a>Nästa steg

- Se språket som användes i [Starter frågor](../samples/starter.md)
- Se avancerade använder i [avancerade frågor](../samples/advanced.md)
- Lär dig att [utforska resurser](explore-resources.md)
