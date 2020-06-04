---
title: Uttrycks verktyg i data flöde för mappning
description: Bygg uttryck genom att använda uttrycks verktyget i att mappa data flöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 23355abdced3a4073cf90ccf60c14af088a4e564
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324173"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Bygg uttryck i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I mappnings data flödet anges många omvandlings egenskaper som uttryck. Dessa uttryck består av kolumn värden, parametrar, funktioner, operatorer och litteraler som utvärderar till data typen Spark vid körning.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Öppna uttrycks verktyget

Gränssnittet för att redigera uttryck i Azure Data Factory användar upplevelse kallas uttrycks verktyg. När du anger din uttrycks logik använder Data Factory [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) -kod komplettering för markering, syntaxkontroll och autokomplettering.

![Uttrycks verktyg](media/data-flow/xpb1.png "Uttrycksverktyget")

I omvandlingar som till exempel härledd kolumn och filter, där uttryck är obligatoriska, öppnar du uttrycks verktyget genom att välja rutan blått uttryck.

![Rutan blå uttryck](media/data-flow/expressionbox.png "Uttrycksverktyget")

När du refererar till kolumner i en matchande eller Group by-villkor kan ett uttryck extrahera värden från kolumner. Om du vill skapa ett uttryck väljer du **beräknad kolumn**.

![Alternativ för beräknad kolumn](media/data-flow/computedcolumn.png "Uttrycksverktyget")

I de fall där ett uttryck eller ett tecken värde är giltiga indata, väljer du **Lägg till dynamiskt innehåll** för att skapa ett uttryck som utvärderas till ett exakt värde.

![Alternativet Lägg till dynamiskt innehåll](media/data-flow/add-dynamic-content.png "Uttrycksverktyget")

## <a name="expression-language-reference"></a>Uttrycks språk referens

Mappning av data flöden har inbyggda funktioner och operatorer som kan användas i uttryck. En lista över tillgängliga funktioner finns [i uttrycks funktioner i mappnings data flödet](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Kolumn namn med specialtecken

Om du har kolumn namn som innehåller specialtecken eller mellanslag, omger du namnet med klammerparenteser för att referera dem i ett uttryck.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Resultat för för hands versions uttryck

Om [fel söknings läge](concepts-data-flow-debug-mode.md) är aktiverat kan du använda Live Spark-klustret för att se en pågående förhands granskning av vad ditt uttryck utvärderar till. När du skapar din logik kan du felsöka ditt uttryck i real tid. 

![Pågående för hands version](media/data-flow/exp4b.png "För hands version av uttrycks data")

Välj **Uppdatera** för att uppdatera resultatet av ditt uttryck mot ett Live-exempel på källan.

![Knappen Uppdatera](media/data-flow/exp5.png "För hands version av uttrycks data")

## <a name="string-interpolation"></a>String-interpolation

Använd citat tecken för att omsluta textuella sträng text tillsammans med uttryck. Du kan inkludera uttrycks funktioner, kolumner och parametrar. String-interpolation är användbart för att undvika en omfattande användning av sträng sammanfogning när parametrar ingår i frågesträngar. Om du vill använda uttrycks syntax, omger du det med klammerparenteser,

Några exempel på String-interpolation:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Kommentars uttryck

Lägg till kommentarer till dina uttryck med hjälp av enradig syntax med en rad och flera rader.

Följande exempel är giltiga kommentarer:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```

Om du lägger till en kommentar överst i uttrycket, visas den i text rutan omvandling för att dokumentera dina omvandlings uttryck.

![Kommentar i text rutan omvandling](media/data-flow/comments2.png "Kommentarer")

## <a name="regular-expressions"></a>Reguljära uttryck

Många uttrycks språk funktioner använder syntaxen för reguljära uttryck. När du använder reguljära uttrycks funktioner försöker uttrycks verktyget tolka ett omvänt snedstreck ( \\ ) som en Escape-teckensekvens. När du använder omvända snedstreck i det reguljära uttrycket, omger du hela regexen i baktick ( \` ) eller använder ett dubbelt omvänt snedstreck.

Ett exempel som använder baktick:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Ett exempel som använder dubbla snedstreck:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Adress mat ris index

Med uttrycks funktioner som returnerar matriser använder du hakparenteser ([]) för att adressera vissa index inuti dessa returnerade mat ris objekt. Matrisen är baserad på dem.

![Expression Builder-matris](media/data-flow/expb2.png "För hands version av uttrycks data")

## <a name="keyboard-shortcuts"></a>Kortkommandon

* CTRL + K CTRL + C: kommentera hela raden.
* CTRL + K CTRL + U: ta bort kommentar.
* F1: ge redigerings hjälp kommandon.
* Alt + nedåtpil: Flytta ned den aktuella raden.
* ALT + UPPIL tangent: flytta upp aktuell rad.
* CTRL + blank steg: Visa Sammanhangs hjälp.

## <a name="convert-to-dates-or-timestamps"></a>Konvertera till datum eller tidsstämplar

Om du vill inkludera sträng litteraler i dina Tidsstämpelns utdata kan du figursätta konverteringen i ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Använd för att konvertera millisekunder från epok till datum eller tidsstämpel `toTimestamp(<number of milliseconds>)` . Om tiden kommer i sekunder multiplicerar du med 1 000.

```toTimestamp(1574127407*1000l)```

Efterföljande "l" i slutet av föregående uttryck avser omvandling till en lång typ som infogad syntax.

## <a name="next-steps"></a>Nästa steg

[Börja bygga data omvandlings uttryck](data-flow-expression-functions.md)
