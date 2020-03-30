---
title: Snabbreferens för KQL
description: En lista över användbara KQL-funktioner och deras definitioner med syntaxexempel.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139087"
---
# <a name="kql-quick-reference"></a>Snabbreferens för KQL

I den här artikeln visas en lista över funktioner och deras beskrivningar som hjälper dig att komma igång med Kusto Query Language.

| Operatör/funktion                               | Beskrivning                           | Syntax                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtrera/söka/villkora**                      |**_Hitta relevanta data genom att filtrera eller söka_** |                      |
| [Där](/azure/kusto/query/whereoperator)                      | Filter på ett visst predikat           | `T | where Predicate`                         |
| [där innehåller/har](/azure/kusto/query/whereoperator)        | `Contains`: Letar efter alla delsträngsmatchning <br> `Has`: Söker efter ett specifikt ord (bättre prestanda)  | `T | where col1 contains/has "[search term]"`|
| [Sök](/azure/kusto/query/searchoperator)                    | Söker i alla kolumner i tabellen efter värdet | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [Ta](/azure/kusto/query/takeoperator)                        | Returnerar det angivna antalet poster. Används för att testa en fråga<br>**_Anmärkning:_** `_take`_ `_limit`och _ är synonymer. | `T | take NumberOfRows` |
| [Fall](/azure/kusto/query/casefunction)                        | Lägger till en villkorssats som liknar om/då/elseif i andra system. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | Skapar en tabell med den distinkta kombinationen av de medföljande kolumnerna i indatatabellen | `distinct [ColumnName], [ColumnName]` |
| **Datum/tid**                                   |**_Åtgärder som använder datum- och tidsfunktioner_**               |                          |
|[Sedan](/azure/kusto/query/agofunction)                           | Returnerar tidsförskjutningen i förhållande till den tid då frågan körs. Det är `ago(1h)` till exempel en timme före den aktuella klockans läsning. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Returnerar data i [olika datumformat](/azure/kusto/query/format-datetimefunction#supported-formats). | `format_datetime(datetime , format)` |
| [Bin](/azure/kusto/query/binfunction)                          | Avrundar alla värden inom en tidsram och grupperar dem | `bin(value,roundTo)` |
| **Skapa/ta bort kolumner**                   |**_Lägga till eller ta bort kolumner i en tabell_** |                                                    |
| [Skriva ut](/azure/kusto/query/printoperator)                      | Matar ut en enda rad med ett eller flera skaläruttryck | `print [ColumnName =] ScalarExpression [',' ...]` |
| [Projekt](/azure/kusto/query/projectoperator)                  | Markerar de kolumner som ska inkluderas i den angivna ordningen | `T | project ColumnName [= Expression] [, ...]` <br> Eller <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projekt-away](/azure/kusto/query/projectawayoperator)         | Väljer de kolumner som ska uteslutas från utdata | `T | project-away ColumnNameOrPattern [, ...]` |
| [Utöka](/azure/kusto/query/extendoperator)                    | Skapar en beräknad kolumn och lägger till den i resultatuppsättningen | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Sortera och aggregera datauppsättning**                 |**_Omstrukturera data genom att sortera eller gruppera dem på ett meningsfullt sätt_**|                  |
| [Sortera](/azure/kusto/query/sortoperator)                        | Sorterar raderna i indatatabellen efter en eller flera kolumner i stigande eller fallande ordning | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Topp](/azure/kusto/query/topoperator)                          | Returnerar de första N-raderna i datauppsättningen när datauppsättningen sorteras med`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Sammanfatta](/azure/kusto/query/summarizeoperator)              | Grupperar raderna efter `by` gruppkolumnerna och beräknar aggregeringar över varje grupp | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [antal](/azure/kusto/query/countoperator)                       | Räknar poster i indatatabellen (till exempel T)<br>Denna operatör är stenografi för`summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator)                        | Sammanfogar raderna med två tabeller för att skapa en ny tabell genom att matcha värden för de angivna kolumnerna från varje tabell. Stöder ett komplett antal `flouter`kopplingstyper: `leftantisemi` `leftouter`, `leftsemi` `inner` `rightanti`, `rightantisemi` `innerunique`, `leftanti`, , , , , , , , , , , , , , , , `rightouter``rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Unionen](/azure/kusto/query/unionoperator)                      | Tar två eller flera tabeller och returnerar alla sina rader | `[T1] | union [T2], [T3], …` |
| [Utbud](/azure/kusto/query/rangeoperator)                      | Genererar en tabell med en aritmetisk värdeserie | `range columnName from start to stop step step` |
| **Formatera data**                                 | **_Omstrukturera data till utdata på ett användbart sätt_** | |
| [Sökning](/azure/kusto/query/lookupoperator)                    | Utökar kolumnerna i en faktatabell med värden som visas upp i en dimensionstabell | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expandera](/azure/kusto/query/mvexpandoperator)               | Förvandlar dynamiska matriser till rader (expansion med flera värden) | `T | mv-expand Column` |
| [Tolka](/azure/kusto/query/parseoperator)                      | Utvärderar ett stränguttryck och tolkar dess värde i en eller flera beräknade kolumner. Används för att strukturera ostrukturerade data. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [gör-serier](/azure/kusto/query/make-seriesoperator)          | Skapar serie angivna aggregerade värden längs en angiven axel | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Låt](/azure/kusto/query/letstatement)                         | Binder ett namn till uttryck som kan referera till dess bundna värde. Värden kan vara lambda-uttryck för att skapa ad hoc-funktioner som en del av frågan. Används `let` för att skapa uttryck över tabeller vars resultat ser ut som en ny tabell. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Allmänt**                                     | **_Diverse operationer och funktioner_** | |
| [Åberopa](/azure/kusto/query/invokeoperator)                    | Kör funktionen på tabellen som den tar emot som indata. | `T | invoke function([param1, param2])` |
| [utvärdera pluginName](/azure/kusto/query/evaluateoperator)     | Utvärderar frågespråktillägg (plugins) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualisering**                               | **_Åtgärder som visar data i ett grafiskt format_** | |
| [Göra](/azure/kusto/query/renderoperator) | Återger resultat som ett grafiskt utdata | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
