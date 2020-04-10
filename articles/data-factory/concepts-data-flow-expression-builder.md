---
title: Uttrycksverktyget vid mappning av dataflöde
description: Skapa uttryck med hjälp av Expression Builder vid mappning av dataflöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 196d917d31eb08af80587bba30d9f7e67bf8cbea
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991713"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Skapa uttryck i mappning av dataflöde

Vid mappning av dataflöde anges många omvandlingsegenskaper som uttryck. Dessa uttryck består av kolumnvärden, parametrar, funktioner, operatorer och litteraler som utvärderas till en Spark-datatyp vid körning.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Öppna uttrycksverktyget

Redigeringsgränssnittet för uttryck i användarupplevelsen i Azure Data Factory kallas Expression Builder. När du anger uttryckslogiken använder Data Factory [IntelliSense-kodkomplettering](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) för markering, syntaxkontroll och automatisk komplettering.

![Uttrycksverktyget](media/data-flow/xpb1.png "Uttrycksverktyget")

I omvandlingar som den härledda kolumnen och filtret, där uttryck är obligatoriska, öppnar Expression Builder genom att markera rutan för blått uttryck.

![Rutan Blått uttryck](media/data-flow/expressionbox.png "Uttrycksverktyget")

När du refererar till kolumner i ett matchande eller grupp-för-villkor kan ett uttryck extrahera värden från kolumner. Om du vill skapa ett uttryck väljer du **Beräknad kolumn**.

![Alternativet Beräknad kolumn](media/data-flow/computedcolumn.png "Uttrycksverktyget")

Om ett uttryck eller ett litteralt värde är giltiga indata väljer du **Lägg till dynamiskt innehåll** för att skapa ett uttryck som utvärderas till ett litteralt värde.

![Alternativet Lägg till dynamiskt innehåll](media/data-flow/add-dynamic-content.png "Uttrycksverktyget")

## <a name="expression-language-reference"></a>Språkreferens för uttryck

Kartläggning av dataflöden har inbyggda funktioner och operatorer som kan användas i uttryck. En lista över tillgängliga funktioner finns [i Uttrycksfunktioner i mappningsdataflödet](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Kolumnnamn med specialtecken

När du har kolumnnamn som innehåller specialtecken eller blanksteg omger du namnet med klammerparenteser för att referera till dem i ett uttryck.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Förhandsgranska uttrycksresultat

Om [felsökningsläget](concepts-data-flow-debug-mode.md) är aktiverat kan du använda det aktiva Spark-klustret för att se en pågående förhandsgranskning av vad uttrycket utvärderas till. När du bygger din logik kan du felsöka ditt uttryck i realtid. 

![Förhandsgranskning av pågående](media/data-flow/exp4b.png "Förhandsgranskning av uttrycksdata")

Välj **Uppdatera** om du vill uppdatera resultatet av uttrycket mot ett direkturval av källan.

![Knappen Uppdatera](media/data-flow/exp5.png "Förhandsgranskning av uttrycksdata")

## <a name="string-interpolation"></a>Interpolering av sträng

Använd citattecken för att omge bokstavlig strängtext tillsammans med uttryck. Du kan inkludera uttrycksfunktioner, kolumner och parametrar. Stränginterpolering är användbart för att undvika omfattande användning av strängsammanfogning när parametrar ingår i frågesträngar. Om du vill använda uttryckssyntax omsluter du den i klammerparenteser,

Några exempel på stränginterpolering:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Kommentarsuttryck

Lägg till kommentarer i dina uttryck med hjälp av enradiga och flerradiga kommentarssyntax.

![Syntax för enrads- och flerradskommentar](media/data-flow/comments.png "Kommentarer")

Följande exempel är giltiga kommentarer:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Om du placerar en kommentar högst upp i uttrycket visas den i textrutan omformning för att dokumentera dina omformningsuttryck.

![Kommentar i textrutan omformning](media/data-flow/comments2.png "Kommentarer")

## <a name="regular-expressions"></a>Reguljära uttryck

Många uttrycksspråkfunktioner använder syntax för reguljära uttryck. När du använder funktioner för reguljära uttryck försöker\\Expression Builder tolka ett omvänt snedstreck ( ) som en escape-teckensekvens. När du använder omvänt snedstreck i ditt reguljära uttryck, antingen omsluta\`hela regex i backticks ( ) eller använda en dubbel omvänt snedstreck.

Ett exempel som använder backticks:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Ett exempel som använder dubbla snedstreck:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Index för adressmatris

Med uttrycksfunktioner som returnerar matriser använder du parenteser ([]) för att adressera specifika index inuti de returmatrisobjekten. Matrisen är baserad på dem.

![Matris för Uttrycksverktyget](media/data-flow/expb2.png "Förhandsgranskning av uttrycksdata")

## <a name="keyboard-shortcuts"></a>Kortkommandon

* Ctrl+K Ctrl+C: Kommentera hela raden.
* Ctrl+K Ctrl+U: Ta av dig en kommenta.
* F1: Ge hjälpkommandon för redigeraren.
* Alt+nedåtpilen: Flytta nedåt den aktuella linjen.
* Alt+Upp piltangent: Flytta upp aktuell linje.
* Ctrl+Blanksteg: Visa sammanhangshjälp.

## <a name="convert-to-dates-or-timestamps"></a>Konvertera till datum eller tidsstämplar

Om du vill inkludera stränglitteraler i tidsstämpelutdataringen sveper du in konverteringen i ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Om du vill konvertera millisekunder från epok till `toTimestamp(<number of milliseconds>)`ett datum eller en tidsstämpel använder du . Om tiden kommer på några sekunder multiplicerar du med 1000.

```toTimestamp(1574127407*1000l)```

Den avslutande "l" i slutet av föregående uttryck betyder konvertering till en lång typ som infogad syntax.

## <a name="next-steps"></a>Nästa steg

[Börja skapa dataomvandlingsuttryck](data-flow-expression-functions.md)
