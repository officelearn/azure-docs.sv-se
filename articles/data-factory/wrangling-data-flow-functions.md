---
title: Datatransformering för data flödes omvandling i Azure Data Factory | Microsoft Docs
description: En översikt över tillgängliga datatransformering data flödes funktioner i Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 73ad0b39e119a9ef33fa1fa595ddcc47f61ae163
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518559"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Omvandlings funktioner i datatransformering Data Flow

Med datatransformering data flöde i Azure Data Factory kan du göra kod fria smidiga data förberedelser och datatransformering i moln skala. Datatransformering data flöde integreras med [Power Query online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) och gör Power Query M-funktioner tillgängliga för data datatransformering via Spark-körning. 

För närvarande stöds inte alla Power Query M-funktioner för data datatransformering trots att de är tillgängliga under redigeringen. När du skapar dina datatransformering-dataflöden uppmanas du att ange följande fel meddelande om en funktion inte stöds:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Nedan visas en lista över Power Query M-funktioner som stöds.

## <a name="column-management"></a>Kolumn hantering

* Markering: [Table. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Borttagning: [Table. RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Byter namn på: [Table. RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table. PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Ordnar om: [Table. ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Rad filtrering

Använd M Function [Table. SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) för att filtrera efter följande villkor:

* Likhet och olikhet
* Numeriska, text-och datum jämförelser (men inte DateTime)
* Numerisk information som [number. IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[udda](https://docs.microsoft.com/powerquery-m/number-iseven)
* Text inne slutning med [text. contains](https://docs.microsoft.com/powerquery-m/text-contains), [text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)eller [text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Datum intervall, inklusive alla "IsIn' [datum funktioner](https://docs.microsoft.com/powerquery-m/date-functions)" 
* Kombinationer av dessa med hjälp av och, eller, eller inte villkor

## <a name="adding-and-transforming-columns"></a>Lägga till och transformera kolumner

Följande M-funktioner lägger till eller transformerar kolumner: [Table. AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table. TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table. ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table. DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Nedan visas de omvandlings funktioner som stöds.

* Numeriskt aritmetiskt
* Text sammanfogning
* Date andTime aritmetiskt (aritmetiska operatorer, [date. AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [date. AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [date. AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [date. AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [date. AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Varaktigheter kan användas för datum-och tids beräkningar, men måste omvandlas till en annan typ innan de skrivs till en mottagare (aritmetiska operatorer, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [duration. dagar](https://docs.microsoft.com/powerquery-m/duration-days), [varaktighet. timmar](https://docs.microsoft.com/powerquery-m/duration-hours), [varaktighet. minuter](https://docs.microsoft.com/powerquery-m/duration-minutes), [ Varaktighet. sekunder](https://docs.microsoft.com/powerquery-m/duration-seconds), [varaktighet. TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [varaktighet. TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [varaktighet. TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [varaktighet. TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* De flesta vanliga, vetenskapliga och trigonometriska numeriska funktioner (alla funktioner under [åtgärder](https://docs.microsoft.com/powerquery-m/number-functions#operations), [avrundning](https://docs.microsoft.com/powerquery-m/number-functions#rounding)och [trigonometriska](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) tecken *utom* Number. fakultet, Number. permutationer och Number. kombination)
* Ersättning ([ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [replacer. ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [text. Replace](https://docs.microsoft.com/powerquery-m/text-replace), [text. Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Extrahering av positions text[(text. PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [text. length](https://docs.microsoft.com/powerquery-m/text-length), [text. start](https://docs.microsoft.com/powerquery-m/text-start), [text. End](https://docs.microsoft.com/powerquery-m/text-end), [text. mitten](https://docs.microsoft.com/powerquery-m/text-middle), [text. ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [text. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Grundläggande textformatering[(text. gemener](https://docs.microsoft.com/powerquery-m/text-lower), [text. versaler](https://docs.microsoft.com/powerquery-m/text-upper), [text. trim](https://docs.microsoft.com/powerquery-m/text-trim)/[starta](https://docs.microsoft.com/powerquery-m/text-trimstart)/[slut](https://docs.microsoft.com/powerquery-m/text-trimend), [text. PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [text. reversed](https://docs.microsoft.com/powerquery-m/text-reverse))
* Datum-/tids funktioner ([datum. dag](https://docs.microsoft.com/powerquery-m/date-day), [datum. månad](https://docs.microsoft.com/powerquery-m/date-month), [datum. Year](https://docs.microsoft.com/powerquery-m/date-year) [Time. Hour](https://docs.microsoft.com/powerquery-m/time-hour), [Time. Minute](https://docs.microsoft.com/powerquery-m/time-minute), [Time. Second](https://docs.microsoft.com/powerquery-m/time-second), [date. DAYOFWEEK](https://docs.microsoft.com/powerquery-m/date-dayofweek), [date. DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [date. DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* If-uttryck (men förgreningar måste ha matchande typer)
* Rad filter som en logisk kolumn
* Tal-, text-, logiska-, datum-och datetime-konstanter

<a name="mergingjoining-tables"></a>Sammanfoga/koppla tabeller
----------------------
* Power Query skapar en kapslad koppling (Table. NestedJoin; användare kan också manuellt skriva [tabellen. AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Användarna måste sedan expandera den kapslade kopplings kolumnen till en icke-kapslad koppling (Table. ExpandTableColumn, stöds inte i någon annan kontext).
* M-funktions [tabellen. koppling](https://docs.microsoft.com/powerquery-m/table-join) kan skrivas direkt för att undvika behovet av ytterligare ett expansions steg, men användaren måste se till att det inte finns några dubbletter av kolumn namn bland de kopplade tabellerna
* Kopplings typer som stöds: [Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Både [Value. equals](https://docs.microsoft.com/powerquery-m/value-equals) och [Value. NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) stöds som jämförelser av nyckel likheter

## <a name="group-by"></a>Gruppera efter

Använd [Table. Group](https://docs.microsoft.com/powerquery-m/table-group) för att aggregera värden.
* Måste användas med en agg regerings funktion
* Sammansättnings funktioner som stöds: [Table. rowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [list. sum](https://docs.microsoft.com/powerquery-m/list-sum), [list. Count](https://docs.microsoft.com/powerquery-m/list-count), [list. genomsnitt](https://docs.microsoft.com/powerquery-m/list-average), [list. min](https://docs.microsoft.com/powerquery-m/list-min), [list. Max](https://docs.microsoft.com/powerquery-m/list-max), [list. StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [list. First](https://docs.microsoft.com/powerquery-m/list-first), list. [Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>ordning

Använd [Table. sort](https://docs.microsoft.com/powerquery-m/table-sort) för att sortera värden.

## <a name="reducing-rows"></a>Minska rader

Behåll och ta bort överkant, Behåll intervallet (motsvarande M-funktioner, endast stödda antal, inte villkor: [Table. firstd](https://docs.microsoft.com/powerquery-m/table-firstn), [Table. Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table. RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table. Range](https://docs.microsoft.com/powerquery-m/table-range), [Table. MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table. MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functionality"></a>Kända funktioner som inte stöds

Nedan visas funktioner som inte stöds. Den här listan är inte uttömmande och kan komma att ändras.
* Sammanfoga kolumner (kan uppnås med AddColumn)
* Dela kolumn
* Lägg till frågor
* ' Använd första raden som rubrik och ' Använd rubriker som första rad '

## <a name="next-steps"></a>Nästa steg
