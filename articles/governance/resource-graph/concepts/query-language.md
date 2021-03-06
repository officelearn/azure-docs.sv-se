---
title: Förstå frågespråket
description: Beskriver resurs diagram tabeller och tillgängliga Kusto data typer, operatorer och funktioner som kan användas med Azure Resource Graph.
ms.date: 11/18/2020
ms.topic: conceptual
ms.openlocfilehash: 34aaaa60ed9d757cc1a63ffaebb2225900cff61f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966691"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Förstå frågespråket i Azure Resource Graph

Frågespråket för Azure Resource Graph stöder ett antal operatorer och funktioner. Varje arbete och drift baserat på [KQL (Kusto Query Language)](/azure/kusto/query/index). Om du vill veta mer om frågespråket som används av resurs diagrammet börjar du med [självstudien för KQL](/azure/kusto/query/tutorial).

Den här artikeln beskriver de språk komponenter som stöds av resurs diagram:

- [Resurs diagram tabeller](#resource-graph-tables)
- [Resurs diagram anpassade språk element](#resource-graph-custom-language-elements)
- [Språk element i KQL som stöds](#supported-kql-language-elements)
- [Frågans omfång](#query-scope)
- [Escape-tecken](#escape-characters)

## <a name="resource-graph-tables"></a>Resurs diagram tabeller

Resurs diagram innehåller flera tabeller för de data som lagras om Azure Resource Manager resurs typer och deras egenskaper. Vissa tabeller kan användas med `join` eller- `union` operatörer för att hämta egenskaper från relaterade resurs typer. Här är listan över tabeller som är tillgängliga i resurs diagram:

|Resurs diagram tabell |Kan `join` ? |Beskrivning |
|---|---|
|Resurser |Ja |Standard tabellen om ingen har definierats i frågan. De flesta resurs typer och egenskaper för Resource Manager finns här. |
|ResourceContainers |Ja |Inkluderar prenumeration (i förhands granskning-- `Microsoft.Resources/subscriptions` ) och resurs typ och data för resurs grupp ( `Microsoft.Resources/subscriptions/resourcegroups` ). |
|AdvisorResources |Nej |Innehåller resurser som är _relaterade_ till `Microsoft.Advisor` . |
|AlertsManagementResources |Nej |Innehåller resurser som är _relaterade_ till `Microsoft.AlertsManagement` . |
|GuestConfigurationResources |Nej |Innehåller resurser som är _relaterade_ till `Microsoft.GuestConfiguration` . |
|MaintenanceResources |Nej |Innehåller resurser som är _relaterade_ till `Microsoft.Maintenance` . |
|PolicyResources |Nej |Innehåller resurser som är _relaterade_ till `Microsoft.PolicyInsights` . (För **hands version**)|
|SecurityResources |Nej |Innehåller resurser som är _relaterade_ till `Microsoft.Security` . |
|ServiceHealthResources |Nej |Innehåller resurser som är _relaterade_ till `Microsoft.ResourceHealth` . |

En fullständig lista, inklusive resurs typer, finns i [referens: tabeller och resurs typer som stöds](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resurser_ är standard tabellen. När du frågar _resurser_ -tabellen, behöver du inte ange tabell namnet om inte `join` eller `union` används. Den rekommenderade metoden är dock att alltid inkludera den första tabellen i frågan.

Använd resurs diagram Utforskaren i portalen för att identifiera vilka resurs typer som är tillgängliga i varje tabell. Alternativt kan du använda en fråga, till exempel `<tableName> | distinct type` för att hämta en lista över resurs typer som den aktuella resurs diagram tabellen stöder, som finns i din miljö.

Följande fråga visar en enkel `join` . Frågeresultatet blandar samman kolumnerna och alla duplicerade kolumn namn från den kopplade tabellen, _ResourceContainers_ i det här exemplet, läggs till med **1**. Eftersom _ResourceContainers_ -tabellen har typer för både prenumerationer och resurs grupper kan du använda någon av typerna för att ansluta till _resursen från resurs_ tabellen.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Följande fråga visar en mer komplex användning av `join` . Frågan begränsar den kopplade tabellen till prenumerations resurser och `project` inkluderar bara det ursprungliga fältet _subscriptionId_ och _namn_ fältet har bytt namn till _undernamn_. Fält namns tillägget undviker `join` att lägga till det som _name1_ eftersom fältet redan finns i _resurser_. Den ursprungliga tabellen filtreras med `where` och följande `project` inkluderar kolumner från båda tabellerna. Frågeresultatet är ett enda nyckel valv som visar typ, namnet på nyckel valvet och namnet på den prenumeration som det finns i.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> När du begränsar `join` resultatet med `project` , måste den egenskap som används av `join` för att relatera de två tabellerna, _subscriptionId_ i ovanstående exempel, ingå i `project` .

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Utökade egenskaper (förhands granskning)

Som _förhands gransknings_ funktion har några av resurs typerna i resurs diagram ytterligare egenskaper som är tillgängliga för frågor utöver de egenskaper som tillhandahålls av Azure Resource Manager. Den här uppsättningen värden, som kallas _utökade egenskaper_, finns på en resurs typ som stöds i `properties.extended` . Om du vill se vilka resurs typer som har _utökade egenskaper_ använder du följande fråga:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Exempel: Hämta antal virtuella datorer genom att `instanceView.powerState.code` :

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Resurs diagram anpassade språk element

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Syntax för delad fråga (förhands granskning)

Som förhands gransknings funktion kan en [delad fråga](../tutorials/create-share-query.md) nås direkt i en resurs diagram fråga. Det här scenariot gör det möjligt att skapa standard frågor som delade frågor och återanvända dem. Använd syntaxen om du vill anropa en delad fråga i en resurs diagram fråga `{{shared-query-uri}}` . URI: n för den delade frågan är _resurs-ID_ för den delade frågan på **inställnings** sidan för den frågan. I det här exemplet är vår URI för delad fråga `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` .
Denna URI pekar på prenumerationen, resurs gruppen och det fullständiga namnet på den delade fråga som vi vill referera till i en annan fråga. Den här frågan är samma som den som skapades i [Självstudier: skapa och dela en fråga](../tutorials/create-share-query.md).

> [!NOTE]
> Det går inte att spara en fråga som refererar till en delad fråga som en delad fråga.

Exempel 1: Använd endast den delade frågan

Resultatet av den här resurs diagram frågan är detsamma som frågan som lagras i den delade frågan.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Exempel 2: inkludera den delade frågan som en del av en större fråga

Den här frågan använder först den delade frågan och använder sedan `limit` för att ytterligare begränsa resultaten.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Språk element i KQL som stöds

Resurs diagram har stöd för en delmängd av KQL- [datatyper](/azure/kusto/query/scalar-data-types/), [skalära funktioner](/azure/kusto/query/scalarfunctions), [skalära operatorer](/azure/kusto/query/binoperators)och [agg regerings funktioner](/azure/kusto/query/any-aggfunction). Vissa [tabell operatörer](/azure/kusto/query/queries) stöds av resurs diagram, varav vissa är olika beteenden.

### <a name="supported-tabulartop-level-operators"></a>Operatorer för huvud-/topp nivå som stöds

Här är listan över KQL tabell operatörer som stöds av resurs diagram med vissa exempel:

|KQL |Exempel fråga för resurs diagram |Kommentarer |
|---|---|---|
|[reparationer](/azure/kusto/query/countoperator) |[Räkna nyckel valv](../samples/starter.md#count-keyvaults) | |
|[kontrollstämpel](/azure/kusto/query/distinctoperator) |[Visa distinkta värden för ett visst alias](../samples/starter.md#distinct-alias-values) | |
|[batteri](/azure/kusto/query/extendoperator) |[Antal virtuella datorer efter OS-typ](../samples/starter.md#count-os) | |
|[ansluta](/azure/kusto/query/joinoperator) |[Nyckel valv med prenumerations namn](../samples/advanced.md#join) |Join-varianter som stöds: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Gräns på 3 `join` i en enskild fråga. Anpassade kopplings strategier, till exempel sändnings anslutning, är inte tillåtna. Information om vilka tabeller som kan användas `join` finns i [resurs diagram tabeller](#resource-graph-tables). |
|[gränserna](/azure/kusto/query/limitoperator) |[Lista över alla offentliga IP-adresser](../samples/starter.md#list-publicip) |Synonymen för `take` . Fungerar inte med [Skip](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Äldre Operator, Använd `mv-expand` i stället. _ROWLIMIT_ max 400. Standardvärdet är 128. |
|[MV-expandera](/azure/kusto/query/mvexpandoperator) |[Lista Cosmos DB med vissa Skriv platser](../samples/advanced.md#mvexpand-cosmosdb) |_ROWLIMIT_ max 400. Standardvärdet är 128. |
|[order](/azure/kusto/query/orderoperator) |[Lista resurser sorterade efter namn](../samples/starter.md#list-resources) |Synonym `sort` |
|[projektfilerna](/azure/kusto/query/projectoperator) |[Lista resurser sorterade efter namn](../samples/starter.md#list-resources) | |
|[projekt bort](/azure/kusto/query/projectawayoperator) |[Ta bort kolumner från resultat](../samples/advanced.md#remove-column) | |
|[ordning](/azure/kusto/query/sortoperator) |[Lista resurser sorterade efter namn](../samples/starter.md#list-resources) |Synonym `order` |
|[sammanfatta](/azure/kusto/query/summarizeoperator) |[Antal Azure-resurser](../samples/starter.md#count-resources) |Endast förenklad första sidan |
|[take](/azure/kusto/query/takeoperator) |[Lista över alla offentliga IP-adresser](../samples/starter.md#list-publicip) |Synonymen för `limit` . Fungerar inte med [Skip](./work-with-data.md#skipping-records). |
|[top](/azure/kusto/query/topoperator) |[Visa de första fem virtuella datorerna efter namn och deras OS-typ](../samples/starter.md#show-sorted) | |
|[Union](/azure/kusto/query/unionoperator) |[Kombinera resultat från två frågor till ett enda resultat](../samples/advanced.md#unionresults) |Enskild tabell tillåts: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _columnName_ - \] _tabell_. Gräns på 3 `union` ben i en enda fråga. Fuzzy-upplösning av `union` ben tabeller är inte tillåten. Kan användas i en enskild tabell eller mellan _resurserna_ och _ResourceContainers_ -tabellerna. |
|[vilken](/azure/kusto/query/whereoperator) |[Visa resurser som innehåller lagring](../samples/starter.md#show-storage) | |

## <a name="query-scope"></a>Frågeomfång

Omfattningen av de prenumerationer som resurserna returneras från, beroende på metoden för åtkomst till resurs diagram. Azure CLI och Azure PowerShell fylla i listan över prenumerationer som ska ingå i begäran baserat på den auktoriserade användarens kontext. Listan över prenumerationer kan definieras manuellt för var och en med parametrarna **prenumerationer** och **prenumerationer** .
I REST API och alla andra SDK: er måste listan över prenumerationer för att inkludera resurser från uttryckligen definieras som en del av begäran.

Som **förhands granskning** lägger REST API-versionen till `2020-04-01-preview` en egenskap för att omfånget av frågan till en [hanterings grupp](../../management-groups/overview.md). Den här för hands versionen av API gör också att prenumerations egenskapen är valfri. Om en hanterings grupp eller en prenumerations lista inte har definierats är frågans omfång alla resurser, vilket innefattar [Azure Lighthouse](../../../lighthouse/concepts/azure-delegated-resource-management.md) -delegerade resurser som den autentiserade användaren kan komma åt. Den nya `managementGroupId` egenskapen tar hanterings gruppens ID, som skiljer sig från namnet på hanterings gruppen. När `managementGroupId` har angetts ingår resurser från de första 5000 prenumerationerna i eller under den angivna hierarkin för hanterings grupper. `managementGroupId` kan inte användas på samma tid som `subscriptions` .

Exempel: fråga alla resurser inom hierarkin för hanterings gruppen med namnet min hanterings grupp med ID myMG.

- REST API-URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Begärandetext

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Escape-tecken

Vissa egenskaps namn, till exempel sådana som innehåller en `.` eller, måste omslutas `$` eller undantas i frågan, eller egenskaps namnet tolkas felaktigt och ger inte det förväntade resultatet.

- `.` – Rad brytning av egenskaps namnet: `['propertyname.withaperiod']`
  
  Exempel fråga som radbryter egenskapen _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` – Escape-tecken i egenskaps namnet. Vilket escape-tecken som används beror på vilket Shell-resurs diagram som körs från.

  - **bash** - `\`

    Exempel fråga som utrymningr egenskaps _\$ typen_ i bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – undanta inte `$` tecken.

  - **PowerShell** - ``` ` ```

    Exempel fråga som utrymningr egenskaps _\$ typen_ i PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Start frågor](../samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md).
- Lär dig mer om hur du [utforskar resurser](explore-resources.md).
