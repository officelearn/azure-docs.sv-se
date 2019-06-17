---
title: Förstå frågespråket
description: Beskriver de tillgängliga Kusto-operatorer och funktioner som kan användas med Azure Resource-diagram.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: dcb21a6aedf16b034fad4f0822e22758dda03c33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800513"
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

## <a name="escape-characters"></a>Escape-tecken

Vissa egenskapsnamn, till exempel de som innehåller en `.` eller `$`, måste omslutna eller undantagna i frågan eller egenskapen namn tolkas felaktigt och ger inte de förväntade resultaten.

- `.` -Omsluta egenskapsnamnet som sådana: `['propertyname.withaperiod']`
  
  Exempelfråga som omsluter egenskapen _OData.Type värdet_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -Escape-tecken i namnet. Escape-tecknet som används beror på gränssnittet Resource Graph körs från.

  - **bash** - `\`

    Exempelfråga som lämnar egenskapen  _\$typ_ i bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -escape-inte den `$` tecken.

  - **PowerShell** - ``` ` ```

    Exempelfråga som lämnar egenskapen  _\$typ_ i PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Nästa steg

- Se språket som användes i [Starter frågor](../samples/starter.md)
- Se avancerade använder i [avancerade frågor](../samples/advanced.md)
- Lär dig att [utforska resurser](explore-resources.md)