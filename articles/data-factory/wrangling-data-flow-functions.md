---
title: Omvandlingsfunktioner för dataflödestransformation i Azure Data Factory
description: En översikt över tillgängliga käbbel dataflödesfunktioner i Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e2517ec4a02a5d61fb3ce1d9ca9ffa2b5f4e8bf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287030"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Omvandlingsfunktioner i käbbel dataflöde

Med dataflödet med käbbel i Azure Data Factory kan du göra kodfria agila dataförberedelser och käbbel i molnskala. Käbbel dataflödet integreras med [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) och gör Power Query M-funktioner tillgängliga för data käbbel via spark körning. 

För närvarande stöds inte alla Power Query M-funktioner för datakäbbel trots att de är tillgängliga under redigeringen. När du skapar dina käbbeldataflöden uppmanas du att få följande felmeddelande om en funktion inte stöds:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Nedan finns en lista över Power Query M-funktioner som stöds.

## <a name="column-management"></a>Hantering av kolumner

* Urval: [Table.SelectKolumner](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Borttagning: [Table.RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Byta namn på: [Tabell.Byt namn På](https://docs.microsoft.com/powerquery-m/table-renamecolumns)namn, [Tabell.PrefixKolumner](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Tabell.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Ändra ordning: [Table.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Radfiltrering

Använd M-funktionen [Table.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) för att filtrera efter följande villkor:

* Jämlikhet och ojämlikhet
* Numeriska jämförelser, text- och datumjämnningar (men inte DateTime)
* Numerisk information som [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Textinneslutning med [Text.Contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)eller [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Datumintervall inklusive alla ["IsIn"-datumfunktioner](https://docs.microsoft.com/powerquery-m/date-functions)) 
* Kombinationer av dessa med hjälp av och, eller inte villkor

## <a name="adding-and-transforming-columns"></a>Lägga till och omforma kolumner

Följande M-funktioner lägger till eller omformar kolumner: [Tabell.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table.TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Nedan finns de omvandlingsfunktioner som stöds.

* Numerisk aritmetik
* Textsammanfogning
* Datum och Tid aritmetiska (aritmetiska operatorer, [Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Varaktigheter kan användas för datum- och tidsaritmetik, men måste omvandlas till en annan typ innan den skrivs till en diskho (aritmetiska operatorer, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Varaktighet.Dagar](https://docs.microsoft.com/powerquery-m/duration-days), [Varaktighet.Timmar](https://docs.microsoft.com/powerquery-m/duration-hours), [Varaktighet.Minuter](https://docs.microsoft.com/powerquery-m/duration-minutes), [Varaktighet.Sekunder](https://docs.microsoft.com/powerquery-m/duration-seconds), [Varaktighet.Totaltdagar](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Varaktighet.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* De flesta standard-, vetenskapliga och trigonometriska numeriska funktioner (Alla funktioner under [Operationer,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [Avrundning](https://docs.microsoft.com/powerquery-m/number-functions#rounding)och [trigonometri](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *utom* Number.Factorial, Number.Permutationer och Number.Combinations)
* Ersättning ([Replacer.ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text.Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text.Remove)](https://docs.microsoft.com/powerquery-m/text-remove)
* Extrahering av positionell text ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Längd](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Mitten](https://docs.microsoft.com/powerquery-m/text-middle), [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Grundläggande textformatering ([Text.Nedre](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Övre,](https://docs.microsoft.com/powerquery-m/text-upper) [Text.Trimma](https://docs.microsoft.com/powerquery-m/text-trim)/[startslut,](https://docs.microsoft.com/powerquery-m/text-trimend)[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/ [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[end](https://docs.microsoft.com/powerquery-m/text-padend), [Text.Omvänd)](https://docs.microsoft.com/powerquery-m/text-reverse)
* Datum-/tidsfunktioner ([datum.dag,](https://docs.microsoft.com/powerquery-m/date-day) [datum.månad,](https://docs.microsoft.com/powerquery-m/date-month) [datum.år](https://docs.microsoft.com/powerquery-m/date-year) [tid.timme,](https://docs.microsoft.com/powerquery-m/time-hour) [tid.minut,](https://docs.microsoft.com/powerquery-m/time-minute) [tid.Sekund](https://docs.microsoft.com/powerquery-m/time-second), [Datum.Dagive,](https://docs.microsoft.com/powerquery-m/date-dayofweek) [Datum.Dagiår](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Datum.DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Om uttryck (men grenar måste ha matchande typer)
* Radfilter som en logisk kolumn
* Konstanter för tal, text, logisk, datum och datumtid

<a name="mergingjoining-tables"></a>Sammanfoga/sammanfoga tabeller
----------------------
* Power Query genererar en kapslad koppling (Table.NestedJoin; användare kan också skriva [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)manuellt ).
    Användarna måste sedan expandera kolumnen kapslad koppling till en icke-kapslad koppling (Table.ExpandTableColumn, stöds inte i någon annan kontext).
* M-funktionen [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) kan skrivas direkt för att undvika behovet av ett ytterligare expansionssteg, men användaren måste se till att det inte finns några dubblettkolumnnamn bland de kopplade tabellerna
* Typer som stöds: [Inner,](https://docs.microsoft.com/powerquery-m/joinkind-inner) [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Både [Value.Equals](https://docs.microsoft.com/powerquery-m/value-equals) och [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) stöds som viktiga likhetsjämförare

## <a name="group-by"></a>Gruppera efter

Använd [Table.Group](https://docs.microsoft.com/powerquery-m/table-group) för att aggregera värden.
* Måste användas med en aggregeringsfunktion
* Aggregeringsfunktioner som stöds: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.Count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max](https://docs.microsoft.com/powerquery-m/list-max), [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Sortering

Använd [Table.Sort](https://docs.microsoft.com/powerquery-m/table-sort) för att sortera värden.

## <a name="reducing-rows"></a>Minska rader

Behåll och ta bort överkant, Behåll intervall (motsvarande M-funktioner, endast stödantal, inte villkor: [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Kända funktioner som inte stöds

| Funktion | Status |
| -- | -- |
| Table.PromoteHeaders | Stöds inte. Samma resultat kan uppnås genom att ange "Första raden som rubrik" i datauppsättningen. |
| Table.CombineColumns | Detta är ett vanligt scenario som inte stöds direkt men som kan uppnås genom att lägga till en ny kolumn som sammanfogar två angivna kolumner.  Tabell.AddColumn(RemoveEmailColumn, "Name", varje [Förnamn] & " & [Efternamn]) |
| Table.TransformColumnTypes | Detta stöds i de flesta fall. Följande scenarier stöds inte: omvandla sträng till valutatyp, omvandla sträng till tidstyp, omvandla sträng till procenttyp. |
| Table.NestedJoin | Bara att göra en koppling kommer att resultera i ett valideringsfel. Kolumnerna måste utökas för att den ska fungera. |
| Table.Distinct | Det går inte att ta bort dubblettrader. |
| Table.RemoveLastN | Ta bort nedersta rader stöds inte. |
| Table.RowCount | Stöds inte, men kan uppnås med en add-kolumn med alla celler tomma (villkorskolumnen kan användas) och sedan använda grupp för i den kolumnen. Table.Group stöds. | 
| Hantering av fel på radnivå | Hantering av fel på radnivå stöds för närvarande inte. Om du till exempel vill filtrera bort icke-numeriska värden från en kolumn är en metod att omvandla textkolumnen till ett tal. Varje cell som inte omvandlas kommer att vara i ett feltillstånd och måste filtreras. Det här scenariot är inte möjligt att käbbel dataflöde. |
| Table.Transpose | Stöds inte |
| Table.Pivot | Stöds inte |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett käbbeldataflöde](wrangling-data-flow-tutorial.md).