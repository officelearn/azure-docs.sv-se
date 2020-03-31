---
title: Förstå frågespråket
description: Beskriver Resursdiagramtabeller och tillgängliga Kusto-datatyper, operatörer och funktioner som kan användas med Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927487"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Förstå frågespråket Azure Resource Graph

Frågespråket för Azure Resource Graph stöder ett antal operatörer och funktioner. Varje arbete och fungerar baserat på [Kusto Query Language (KQL)](/azure/kusto/query/index). Om du vill veta mer om frågespråket som används av Resource Graph börjar du med [självstudien för KQL](/azure/kusto/query/tutorial).

Den här artikeln beskriver de språkkomponenter som stöds av Resource Graph:

- [Resursdiagramtabeller](#resource-graph-tables)
- [KQL-språkelement som stöds](#supported-kql-language-elements)
- [Escape tecken](#escape-characters)

## <a name="resource-graph-tables"></a>Resursdiagramtabeller

Resource Graph innehåller flera tabeller för de data som lagras om Resource Manager-resurstyper och deras egenskaper. Dessa tabeller kan `join` användas `union` med eller operatorer för att hämta egenskaper från relaterade resurstyper. Här är listan över tabeller som är tillgängliga i Resursdiagram:

|Resursdiagramtabeller |Beskrivning |
|---|---|
|Resurser |Standardtabellen om ingen har definierats i frågan. De flesta resurshanterarens resurstyper och egenskaper finns här. |
|Resurscontainers |Inkluderar prenumeration (i `Microsoft.Resources/subscriptions`förhandsversion --`Microsoft.Resources/subscriptions/resourcegroups`) och resursgrupp ( ) resurstyper och data. |
|AdvisorResources |Inkluderar _related_ resurser `Microsoft.Advisor`relaterade till . |
|AviseringarManagementResurser |Inkluderar _related_ resurser `Microsoft.AlertsManagement`relaterade till . |
|UnderhållResurser |Inkluderar _related_ resurser `Microsoft.Maintenance`relaterade till . |
|SecurityResources |Inkluderar _related_ resurser `Microsoft.Security`relaterade till . |

En fullständig lista med resurstyper finns i [Referens: Tabeller och resurstyper som stöds](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resurser_ är standardtabellen. När du frågar efter tabellen _Resurser_ behöver den inte `join` ange `union` tabellnamnet om inte eller används. Den rekommenderade övningen är dock att alltid inkludera den ursprungliga tabellen i frågan.

Använd Resource Graph Explorer i portalen för att ta reda på vilka resurstyper som är tillgängliga i varje tabell. Som ett alternativ kan du `<tableName> | distinct type` använda en fråga som för att få en lista över resurstyper som den angivna resursdiagramtabellen stöder som finns i din miljö.

Följande fråga visar `join`en enkel . Frågeresultatet blandar kolumnerna tillsammans och eventuella dubblettkolumnnamn från den kopplade _tabellen, ResourceContainers_ i det här exemplet, läggs till med **1**. Eftersom _resourceContainers-tabellen_ har typer för både prenumerationer och resursgrupper kan någon av typerna användas för att ansluta till resursen från _resurstabellen._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Följande fråga visar en mer `join`komplex användning av . Frågan begränsar den kopplade tabellen till `project` prenumerationsresurser och med att endast inkludera den ursprungliga _fältprenumerationenId_ och _namnfältet_ som bytt namn till _Undernamn_. Fältet byter namn `join` undviker att lägga till det som _namn1_ eftersom fältet redan finns i _Resurser_. Den ursprungliga tabellen filtreras `where` med `project` och följande innehåller kolumner från båda tabellerna. Frågeresultatet är ett enda nyckelvalv som visar typ, namnet på nyckelvalvet och namnet på prenumerationen det finns i.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> `join` När du begränsar `project`resultaten med måste `join` egenskapen som används för att relatera de två `project`tabellerna, _subscriptionId_ i exemplet ovan inkluderas i .

## <a name="supported-kql-language-elements"></a>KQL-språkelement som stöds

Resource Graph stöder alla [KQL-datatyper,](/azure/kusto/query/scalar-data-types/) [skalärfunktioner,](/azure/kusto/query/scalarfunctions) [skaläroperatorer](/azure/kusto/query/binoperators)och [aggregeringsfunktioner](/azure/kusto/query/any-aggfunction). Specifika [tabelloperatorer](/azure/kusto/query/queries) stöds av Resource Graph, varav vissa har olika beteenden.

### <a name="supported-tabulartop-level-operators"></a>Tabell-/toppnivåoperatorer som stöds

Här är listan över KQL-tabelloperatorer som stöds av Resource Graph med specifika exempel:

|KQL (KQL) |Exempelfråga för resursdiagram |Anteckningar |
|---|---|---|
|[antal](/azure/kusto/query/countoperator) |[Räkna nyckelvalv](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Visa distinkta värden för ett visst alias](../samples/starter.md#distinct-alias-values) | |
|[Utöka](/azure/kusto/query/extendoperator) |[Antal virtuella datorer efter OS-typ](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Nyckelvalv med prenumerationsnamn](../samples/advanced.md#join) |Gå med smaker som stöds: [innerunique,](/azure/kusto/query/joinoperator#default-join-flavor) [inre,](/azure/kusto/query/joinoperator#inner-join) [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Gräns 3 `join` i en enda fråga. Anpassade kopplingsstrategier, till exempel broadcast-koppling, är inte tillåtna. Kan användas i en enda tabell eller mellan tabellerna _Resurser_ och _ResourceContainers._ |
|[Gräns](/azure/kusto/query/limitoperator) |[Lista över alla offentliga IP-adresser](../samples/starter.md#list-publicip) |Synonymen med`take` |
|[mvexpand (mvexpand)](/azure/kusto/query/mvexpandoperator) | | Äldre operator, `mv-expand` använd i stället. _RowLimit_ max 400. Standardvärdet är 128. |
|[mv-expandera](/azure/kusto/query/mvexpandoperator) |[Lista Cosmos DB med specifika skrivplatser](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max 400. Standardvärdet är 128. |
|[Ordning](/azure/kusto/query/orderoperator) |[Lista resurser sorterade efter namn](../samples/starter.md#list-resources) |Synonymen med`sort` |
|[Projekt](/azure/kusto/query/projectoperator) |[Lista resurser sorterade efter namn](../samples/starter.md#list-resources) | |
|[projekt-away](/azure/kusto/query/projectawayoperator) |[Ta bort kolumner från resultat](../samples/advanced.md#remove-column) | |
|[Sortera](/azure/kusto/query/sortoperator) |[Lista resurser sorterade efter namn](../samples/starter.md#list-resources) |Synonymen med`order` |
|[Sammanfatta](/azure/kusto/query/summarizeoperator) |[Antal Azure-resurser](../samples/starter.md#count-resources) |Förenklad endast första sida |
|[Ta](/azure/kusto/query/takeoperator) |[Lista över alla offentliga IP-adresser](../samples/starter.md#list-publicip) |Synonymen med`limit` |
|[Topp](/azure/kusto/query/topoperator) |[Visa de första fem virtuella datorerna efter namn och deras OS-typ](../samples/starter.md#show-sorted) | |
|[Unionen](/azure/kusto/query/unionoperator) |[Kombinera resultat från två frågor till ett enda resultat](../samples/advanced.md#unionresults) |Tillåten tabell: _Tabell_ _för Kolumnnamn_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=`i T _ColumnName_ \] . Gräns på `union` 3 ben i en enda fråga. Luddig upplösning `union` på bentabeller är inte tillåtet. Kan användas i en enda tabell eller mellan tabellerna _Resurser_ och _ResourceContainers._ |
|[Där](/azure/kusto/query/whereoperator) |[Visa resurser som innehåller lagring](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape tecken

Vissa egenskapsnamn, till exempel `.` `$`de som innehåller en eller , måste radbrytas eller skickas i frågan eller egenskapsnamnet tolkas felaktigt och ger inte de förväntade resultaten.

- `.`- Radbryt egenskapsnamnet som sådant:`['propertyname.withaperiod']`
  
  Exempelfråga som radbryts i _egenskapen odata.type:_

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- Undvik tecknet i egenskapsnamnet. Vilket escape-tecken som används beror på att skalresursdiagrammet körs från.

  - **Bash** - `\`

    Exempelfråga som undgår _ \$egenskapstypen_ i bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Undvik inte `$` tecknet.

  - **Powershell** - ``` ` ```

    Exempelfråga som undgår _ \$egenskapstypen_ i PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Starter-frågor](../samples/starter.md).
- Se avancerade användningsområden i [avancerade frågor](../samples/advanced.md).
- Läs mer om hur du [utforskar resurser](explore-resources.md).