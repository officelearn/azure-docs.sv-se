---
title: Förstå frågespråket
description: Beskriver tillgängliga Kusto-operatörer och funktioner som kan användas med Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231509"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Förstå frågespråket i Azure Resource Graph

Frågespråket för Azure Resource Graph stöder ett antal operatorer och funktioner. Varje arbete och drift baserat på [Azure datautforskaren](../../../data-explorer/data-explorer-overview.md).

Det bästa sättet att lära sig om frågespråket som används av resurs diagram är att börja med dokumentationen för språket för Azure Datautforskaren- [frågor](/azure/kusto/query/index). Det ger en förståelse för hur språket är strukturerat och hur de olika operatörer och funktioner som stöds fungerar tillsammans.

## <a name="supported-tabular-operators"></a>Tabell operatörer som stöds

Här är listan över tabell operatörer som stöds i resurs diagram:

- [antal](/azure/kusto/query/countoperator)
- [kontrollstämpel](/azure/kusto/query/distinctoperator)
- [batteri](/azure/kusto/query/extendoperator)
- [gränserna](/azure/kusto/query/limitoperator)
- [Sortera efter](/azure/kusto/query/orderoperator)
- [projektfilerna](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [urvalsundersökningar](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [Sortera efter](/azure/kusto/query/sortoperator)
- [sammanfatta](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [översta](/azure/kusto/query/topoperator)
- [översta kapslade](/azure/kusto/query/topnestedoperator)
- [Top-Hitters](/azure/kusto/query/tophittersoperator)
- [vilken](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funktioner som stöds

Här är en lista över funktioner som stöds i resurs diagram:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip ()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Escape-tecken

Vissa egenskaps namn, till exempel sådana som innehåller `.` en `$`eller, måste omslutas eller undantas i frågan, eller egenskaps namnet tolkas felaktigt och ger inte det förväntade resultatet.

- `.`– Rad brytning av egenskaps namnet:`['propertyname.withaperiod']`
  
  Exempel fråga som radbryter egenskapen _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`– Escape-tecken i egenskaps namnet. Vilket escape-tecken som används beror på vilket Shell-resurs diagram som körs från.

  - **bash** - `\`

    Exempel fråga som utrymningr egenskaps  _\$typen_ i bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – undanta `$` inte tecken.

  - **PowerShell** - ``` ` ```

    Exempel fråga som utrymningr egenskaps  _\$typen_ i PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Start frågor](../samples/starter.md)
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md)
- Lär dig att [utforska resurser](explore-resources.md)