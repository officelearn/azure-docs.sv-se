---
title: Förstå frågespråket
description: Beskriver de tillgängliga Kusto-operatorer och funktioner som kan användas med Azure Resource-diagram.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 08e4f09665a3501073f55b7f5b82bf51cf508ea9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276685"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Förstå Azure Resource Graph-frågespråk

Frågespråket i Azure Resource Graph stöder ett antal operatorer och funktioner. Varje fungerar och driva utifrån [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md).

Det bästa sättet att lära dig frågespråket som används av resursen Graph är att börja med dokumentation för Azure Data Explorer [frågespråk](/azure/kusto/query/index). Det ger en förståelse om hur språket är strukturerad och hur de olika stöds operatorer och funktioner fungerar tillsammans.

## <a name="supported-tabular-operators"></a>Tabular operatorer som stöds

Här är listan över tabular operatorer som stöds i resursen Graph:

- [antal](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [Utöka](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by-](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
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
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="next-steps"></a>Nästa steg

- Se språket som användes i [Starter frågor](../samples/starter.md)
- Se avancerade använder i [avancerade frågor](../samples/advanced.md)
- Lär dig att [utforska resurser](explore-resources.md)