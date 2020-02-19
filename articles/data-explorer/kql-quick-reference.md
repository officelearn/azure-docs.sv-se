---
title: Snabb referens för KQL
description: En lista över användbara KQL-funktioner och deras definitioner med exempel på syntax.
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: b53890afd10a3aee131ab3897d01e6b6fdf261a6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429272"
---
# <a name="kql-quick-reference"></a>Snabb referens för KQL

Den här artikeln visar en lista över funktioner och beskrivningar som hjälper dig att komma igång med att använda Kusto-frågespråk.

| Operator/funktion                               | Beskrivning                           | Syntax                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filter/Sök/villkor**                      |**_Hitta relevanta data genom att filtrera eller söka_** |                      |
| [vilken](/azure/kusto/query/whereoperator.md)                      | Filter för ett speciellt predikat           | `T | where Predicate`                         |
| [där innehåller/har](/azure/kusto/query/whereoperator.md)        | `Contains`: söker efter en under Strängs matchning <br> `Has`: söker efter ett speciellt ord (bättre prestanda)  | `T | where col1 contains/has "[search term]"`|
| [Sök](/azure/kusto/query/searchoperator.md)                    | Söker igenom alla kolumner i tabellen efter värdet | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [gå](/azure/kusto/query/takeoperator.md)                        | Returnerar det angivna antalet poster. Används för att testa en fråga<br>**_Obs_** : `_take`_ och `_limit`_ är synonymer. | `T | take NumberOfRows` |
| [enskilt](/azure/kusto/query/casefunction.md)                        | Lägger till en villkors instruktion som liknar if/then/ElseIf i andra system. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [kontrollstämpel](/azure/kusto/query/distinctoperator.md)                | Skapar en tabell med en distinkt kombination av de angivna kolumnerna i indata-tabellen | `distinct [ColumnName], [ColumnName]` |
| **Datum/tid**                                   |**_Åtgärder som använder datum-och tids funktioner_**               |                          |
|[sen](/azure/kusto/query/agofunction.md)                           | Returnerar tids förskjutningen i förhållande till den tid då frågan körs. `ago(1h)` är till exempel en timme före läsningen av den aktuella klockan. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction.md)  | Returnerar data i [olika datum format](/azure/kusto/query/format-datetimefunction.md#supported-formats). | `format_datetime(datetime , format)` |
| [plats](/azure/kusto/query/binfunction.md)                          | Avrundar alla värden i en tidsram och grupper dem | `bin(value,roundTo)` |
| **Skapa/ta bort kolumner**                   |**_Lägga till eller ta bort kolumner i en tabell_** |                                                    |
| [utskriftsvy](/azure/kusto/query/printoperator.md)                      | Matar ut en enskild rad med ett eller flera skalära uttryck | `print [ColumnName =] ScalarExpression [',' ...]` |
| [projektfilerna](/azure/kusto/query/projectoperator.md)                  | Markerar de kolumner som ska tas med i den angivna ordningen | `T | project ColumnName [= Expression] [, ...]` <br> Eller <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projekt bort](/azure/kusto/query/projectawayoperator.md)         | Markerar de kolumner som ska undantas från utdata | `T | project-away ColumnNameOrPattern [, ...]` |
| [batteri](/azure/kusto/query/extendoperator.md)                    | Skapar en beräknad kolumn och lägger till den i resultat uppsättningen | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Sortera och aggregera data uppsättning**                 |**_Strukturera om data genom att sortera eller gruppera dem på ett meningsfullt sätt_**|                  |
| [ordning](/azure/kusto/query/sortoperator.md)                        | Sorterar raderna i indatatypen efter en eller flera kolumner i stigande eller fallande ordning | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [översta](/azure/kusto/query/topoperator.md)                          | Returnerar de första N raderna i data uppsättningen när data uppsättningen sorteras med hjälp av `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [sammanfatta](/azure/kusto/query/summarizeoperator.md)              | Grupperar raderna enligt `by` grupp kolumner och beräknar agg regeringar över varje grupp | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [antal](/azure/kusto/query/countoperator.md)                       | Räknar poster i indatacellen (t. ex. T)<br>Den här operatorn är kort för `summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator.md)                        | Sammanfogar raderna i två tabeller för att skapa en ny tabell genom att matcha värdena för de angivna kolumnerna från varje tabell. Har stöd för ett stort antal kopplings typer: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Union](/azure/kusto/query/unionoperator.md)                      | Tar två eller flera tabeller och returnerar alla rader | `[T1] | union [T2], [T3], …` |
| [intervall](/azure/kusto/query/rangeoperator.md)                      | Genererar en tabell med en aritmetisk värde serie | `range columnName from start to stop step step` |
| **Formatera data**                                 | **_Strukturera om data till utdata på ett användbart sätt_** | |
| [sökning](/azure/kusto/query/lookupoperator.md)                    | Utökar kolumnerna i en fakta tabell med värden som slås upp i en dimensions tabell | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [MV-expandera](/azure/kusto/query/mvexpandoperator.md)               | Växlar dynamiska matriser till rader (flera värdes expansion) | `T | mv-expand Column` |
| [inte](/azure/kusto/query/parseoperator.md)                      | Utvärderar ett sträng uttryck och tolkar värdet i en eller flera beräknade kolumner. Används för att strukturera ostrukturerade data. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [Skapa-serien](/azure/kusto/query/make-seriesoperator.md)          | Skapar en serie med angivna aggregerade värden längs en angiven axel | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [medför](/azure/kusto/query/letstatement.md)                         | Binder ett namn till uttryck som kan referera till det bundna värdet. Värden kan vara lambda-uttryck för att skapa ad hoc-funktioner som en del av frågan. Använd `let` för att skapa uttryck över tabeller vars resultat ser ut som en ny tabell. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Allmänt**                                     | **_Diverse åtgärder och funktion_** | |
| [låta](/azure/kusto/query/invokeoperator.md)                    | Kör funktionen på den tabell som den tar emot som inmatade. | `T | invoke function([param1, param2])` |
| [utvärdera pluginName](/azure/kusto/query/evaluateoperator.md)     | Utvärderar frågespråk för språk tillägg (plugin-program) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visuella**                               | **_Åtgärder som visar data i ett grafiskt format_** | |
| [återge](/azure/kusto/query/renderoperator.md) | Återger resultat som grafisk utdata | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
