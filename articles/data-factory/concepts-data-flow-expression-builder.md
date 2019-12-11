---
title: Uttrycks verktyg i data flöde för mappning
description: Bygg uttryck med uttrycks verktyget i att mappa data flöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969403"
---
# <a name="building-expressions-in-mapping-data-flow"></a>Skapa uttryck i data flöde för mappning

I mappnings data flödet anges många omvandlings egenskaper som uttryck. Dessa uttryck består av kolumn värden, parametrar, funktioner, operatorer och litteraler som utvärderar till data typen Spark vid körning.

## <a name="opening-the-expression-builder"></a>Öppna uttrycks verktyget

Gränssnittet för att redigera uttrycket i Data Factory-UXen är känt som **uttrycks verktyget**. När du anger i din uttrycks logik använder Data Factory [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) -kod komplettering för markering, syntaxkontroll och Autoslutför.

![Uttrycks verktyg](media/data-flow/xpb1.png "Uttrycksverktyget")

I omvandlingar som till exempel härledd kolumn och filter, där-uttryck är obligatoriska, öppnar du uttrycks verktyget genom att klicka på rutan blå uttryck.

![Uttrycks verktyg](media/data-flow/expressionbox.png "Uttrycksverktyget")

När du refererar till kolumner i en matchande eller Group by-villkor kan ett uttryck extrahera värden från kolumner. Om du vill skapa ett uttryck väljer du alternativet beräknad kolumn.

![Uttrycks verktyg](media/data-flow/computedcolumn.png "Uttrycksverktyget")

I de fall där ett uttryck eller ett litteralt värde är giltiga indata, kan du använda Lägg till dynamiskt innehåll för att skapa ett uttryck som utvärderas till en literal.

![Uttrycks verktyg](media/data-flow/add-dynamic-content.png "Uttrycksverktyget")

## <a name="expression-language-reference"></a>Uttrycks språk referens

Mappning av data flöden har inbyggda funktioner och operatorer som kan användas i uttryck. En lista över tillgängliga funktioner finns på språk referens sidan [mappa data flödes uttryck](data-flow-expression-functions.md) .

## <a name="column-names-with-special-characters"></a>Kolumn namn med specialtecken

Om du har kolumn namn som innehåller specialtecken eller mellanslag, omger du namnet med klammerparenteser för att referera dem i ett uttryck.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>Uttrycks resultat för för hands Grans kar

Om [fel söknings läge](concepts-data-flow-debug-mode.md) är aktiverat kan du använda Live Spark-klustret för att se en pågående förhands granskning av vad ditt uttryck utvärderar till. När du skapar din logik kan du felsöka ditt uttryck i real tid. 

![Uttrycks verktyg](media/data-flow/exp4b.png "För hands version av uttrycks data")

Klicka på Uppdatera om du vill uppdatera resultatet av ditt uttryck mot ett Live-exempel på källan.

![Uttrycks verktyg](media/data-flow/exp5.png "För hands version av uttrycks data")

## <a name="string-interpolation"></a>String-interpolation

Använd dubbla citat tecken för att omsluta textuella sträng text med uttryck. Du kan inkludera uttrycks funktioner, kolumner och parametrar. String-interpolation är användbart för att undvika en omfattande användning av sträng sammanfogning när du inkluderar parametrar i frågesträngar. Om du vill använda uttrycks syntax, omger du det med klammerparenteser,

Några exempel på String-interpolation:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>Kommentera uttryck

Lägg till kommentarer till dina uttryck med hjälp av en enkel rad och en kommentar med flera rader:

![Kommentarer](media/data-flow/comments.png "Kommentarer")

Nedan visas exempel på giltiga kommentarer:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Om du lägger till en kommentar överst i uttrycket visas den i text rutan omvandling för att dokumentera dina omvandlings uttryck:

![Kommentarer](media/data-flow/comments2.png "Kommentarer")

## <a name="regular-expressions"></a>Reguljära uttryck

Många uttrycks språk funktioner använder syntaxen för reguljära uttryck. När du använder reguljära uttrycks funktioner försöker uttrycks verktyget tolka omvänt snedstreck (\\) som en Escape-teckensekvens. När du använder omvända snedstreck i det reguljära uttrycket, omger du hela regexen i Ticket (\`) eller använder ett dubbelt omvänt snedstreck.

Exempel med Tick

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

eller med dubbla snedstreck

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Adressering av mat ris index

Med uttrycks funktioner som returnerar matriser, använder du hakparenteser [] för att adressera vissa index inuti det returnerade mat ris objektet. Matrisen är en-baserad.

![Expression Builder-matris](media/data-flow/expb2.png "För hands version av uttrycks data")

## <a name="keyboard-shortcuts"></a>Kortkommandon

* ```Ctrl-K Ctrl-C```: hela raden kommentarer
* ```Ctrl-K Ctrl-U```: ta bort kommentar
* ```F1```: ge redigerings hjälp kommandon
* ```Alt-Down Arrow```: Flytta ned aktuell rad
* ```Alt-Up Arrow```: flytta upp aktuell rad
* ```Cntrl-Space```: Visa Sammanhangs hjälp

## <a name="convert-to-dates-or-timestamps"></a>Konvertera till datum eller tidsstämplar

Om du vill inkludera sträng litteraler i dina tidsstämplar måste du figursätta konverteringen i ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Använd `toTimestamp(<number of milliseconds>)`om du vill konvertera millisekunder från epok till datum eller tidsstämpel. Om tiden kommer i sekunder multiplicerar du med 1000.

```toTimestamp(1574127407*1000l)```

Efterföljande "l" i slutet av uttrycket ovan betyder omvandling till en lång typ som infogad syntax.

## <a name="next-steps"></a>Nästa steg

[Börja bygga data omvandlings uttryck](data-flow-expression-functions.md)
