---
title: Listningsparametrar för Azure Monitor-arbetsbok
description: Förenkla komplex rapportering med fördefinierade och anpassade parameteriserade arbetsböcker som innehåller rullgardinsparametrar
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658293"
---
# <a name="workbook-drop-down-parameters"></a>Parametrar för arbetsboksrullgardero

Nedrullningsbara enheter gör det möjligt för användaren att samla in ett eller flera indatavärden från en känd uppsättning (välj till exempel en av appens begäranden). Rullgardinsmenyn är ett användarvänligt sätt att samla in godtyckliga indata från användare. Listrutan är särskilt användbar när det gäller att aktivera filtrering i dina interaktiva rapporter. 

Det enklaste sättet att ange en listruta är genom att tillhandahålla en statisk lista i parameterinställningen. Ett mer intressant sätt är att få listan dynamiskt via en KQL fråga. Med parameterinställningarna kan du också ange om den är enkel eller flervals, och om den är flervals, hur resultatuppsättningen ska formateras (avgränsare, offert osv.).

## <a name="creating-a-static-drop-down-parameter"></a>Skapa en statisk listruta

1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn:`Environment`
    2. Parametertyp:`Drop down`
    3. Krävs:`checked`
    4. Tillåt: `multiple selection``unchecked`
    5. Hämta data från:`JSON`
5. I textblocket JSON-inmatning infogar du det här json-kodavsnittet:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Tryck på `Update` den blå knappen.
7. Välj Spara i verktygsfältet för att skapa parametern.
8. Parametern Miljö kommer att vara en listruta med de tre värdena.

    ![Bild som visar skapandet av en statisk drunkning](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Skapa en statisk listruta med grupper av objekt
Om frågeresultatet/json-fältet innehåller ett "grupp"-fält visas grupper av värden i listrutan. Följ ovanstående prov, men använd följande json istället:
```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Skapa en dynamisk listruta
1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn:`RequestName`
    2. Parametertyp:`Drop down`
    3. Krävs:`checked`
    4. Tillåt: `multiple selection``unchecked`
    5. Hämta data från:`Query`
5. I textblocket JSON-inmatning infogar du det här json-kodavsnittet:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Tryck på `Run Query` den blå knappen.
2. Välj Spara i verktygsfältet för att skapa parametern.
3. Parametern RequestName kommer att vara en listruta med namnen på alla begäranden i appen.

    ![Bild som visar skapandet av en dynamisk listruta](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Parametern Referera till listrutan
### <a name="in-kql"></a>I KQL
1. Lägg till en frågekontroll i arbetsboken och välj en Application Insights-resurs.
2. I KQL-redigeraren anger du det här kodavsnittet

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Detta utökar frågeutvärderingstiden till:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Kör fråga för att se resultaten. Du kan också återge den som ett diagram.

    ![Bild som visar en nedrullningsbar i KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Parametervärde, etikett, markering och grupp
Frågan som används i den dynamiska listrutan ovan returnerar bara en lista med värden som återges troget i listrutan. Men vad händer om du vill ha ett annat visningsnamn, eller något av dessa som ska väljas? Nedrullningsparametrar tillåter detta via värde-, etikett-, markerings- och gruppkolumnerna.

Exemplet nedan visar hur du får en lista över Application Insights-beroenden vars visningsnamn är utformade med en emoji, har den första markerad och grupperas efter operationsnamn.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Alternativ för listruta
| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Det valda värdet | FÅ fabrikamaccount |
| `{DependencyName:label}` | Den valda etiketten | 🌐 FÅ fabrikamaccount |
| `{DependencyName:value}` | Det valda värdet | FÅ fabrikamaccount |

## <a name="multiple-selection"></a>Flera val
Exemplen hittills anger uttryckligen parametern att välja endast ett värde i listrutan. Rullgardinsmenyn `multiple selection` stöder också - aktivera detta `Allow multiple selection` är så enkelt som att kontrollera alternativet. 

Användaren har också möjlighet att ange formatet på resultatet `delimiter` `quote with` som anges via och inställningar. Standardvärdet returnerar bara värdena som en samling i det här formuläret: "a", "b", "c". De har också möjlighet att begränsa antalet val.

Den KQL refererar till parametern måste ändras för att arbeta med formatet för resultatet. Det vanligaste sättet att aktivera `in` det är via operatören.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Här är ett exempel för listrutan för flera väljer på jobbet:

![Bild som visar en listruta med flera markeringar](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
