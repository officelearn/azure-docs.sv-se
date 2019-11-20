---
title: Azure Data Factory mappar uttrycks verktyg för data flöde
description: Uttrycks verktyget för Azure Data Factory mappning av data flöden
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 3664a7c311e15ce3aa61fc71f98a46e3f2618143
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184689"
---
# <a name="mapping-data-flow-expression-builder"></a>Mappa data flöde uttrycks verktyg



I Azure Data Factory mappa data flödet hittar du resultat rutor där du kan ange uttryck för data omvandling. Använd kolumner, fält, variabler, parametrar, funktioner från ditt data flöde i dessa rutor. Om du vill bygga uttrycket använder du uttrycks verktyget, som startas genom att klicka i text rutan uttryck inuti omvandlingen. Ibland kan du också se alternativen för beräknad kolumn när du väljer kolumner för omvandling. När du klickar på den visas även uttrycks verktyget.

![Uttrycks verktyg](media/data-flow/xpb1.png "Uttrycksverktyget")

Uttrycks verktyget standard är standard alternativet text redigerare. funktionen för automatisk komplettering läser från hela Azure Data Factory data flödes objekt modell med syntaxkontroll och markering.

![Slutför uttrycks verktyg automatiskt](media/data-flow/expb1.png "Slutför uttrycks verktyg automatiskt")

## <a name="build-schemas-in-output-schema-pane"></a>Bygg scheman i fönstret utdata schema

![Lägg till komplex kolumn](media/data-flow/complexcolumn.png "Lägg till kolumner")

I fönstret till vänster schema för utdata visas de kolumner som du ändrar och lägger till i schemat. Du kan bygga enkla och komplexa data strukturer interaktivt här. Lägg till ytterligare fält med hjälp av "Lägg till kolumn" och skapa hierarkier med "Lägg till underkolumn".

![Lägg till under kolumn](media/data-flow/addsubcolumn.png "Lägg till under kolumn")

## <a name="data-preview-in-debug-mode"></a>Förhandsgranska data i fel söknings läge

![Uttrycks verktyg](media/data-flow/exp4b.png "För hands version av uttrycks data")

När du arbetar med dina data flödes uttryck växlar du till fel söknings läge från den Azure Data Factory data flödets design yta, vilket möjliggör en pågående för hands version av data resultaten från det uttryck som du skapar. Direkt fel sökning i real tid har Aktiver ATS för dina uttryck.

![Fel söknings läge](media/data-flow/debugbutton.png "Knappen Felsök")

Klicka på Uppdatera om du vill uppdatera resultatet av ditt uttryck mot ett Live-exempel på källan i real tid.

![Uttrycks verktyg](media/data-flow/exp5.png "För hands version av uttrycks data")

## <a name="comments"></a>Kommentarer

Lägg till kommentarer till dina uttryck med hjälp av en enkel rad och en kommentar med flera rader:

![Kommentarer](media/data-flow/comments.png "Kommentarer")

## <a name="regular-expressions"></a>Reguljära uttryck

Det Azure Data Factory data flödets uttrycks språk, [fullständig referens dokumentation här](https://aka.ms/dataflowexpressions), aktiverar funktioner som inkluderar syntax för reguljära uttryck. När du använder reguljära uttrycks funktioner försöker uttrycks verktyget tolka omvänt snedstreck (\\) som en Escape-teckensekvens. När du använder omvända snedstreck i det reguljära uttrycket, omger du hela regexen i Ticket (\`) eller använder ett dubbelt omvänt snedstreck.

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

## <a name="handling-names-with-special-characters"></a>Hantera namn med specialtecken

När du har kolumn namn som innehåller specialtecken eller mellanslag, omger du namnet med klammerparenteser.
* ```{[dbo].this_is my complex name$$$}```

## <a name="keyboard-shortcuts"></a>Kortkommandon

* ```Ctrl-K Ctrl-C```: hela raden kommentarer
* ```Ctrl-K Ctrl-U```: ta bort kommentar
* ```F1```: ge redigerings hjälp kommandon
* ```Alt-Down Arrow```: flytta aktuell rad nedåt
* ```Alt-Up Arrow```: flytta upp aktuell rad
* ```Cntrl-Space```: Visa Sammanhangs hjälp

## <a name="manual-comments"></a>Manuella kommentarer

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Om du lägger till en kommentar överst i uttrycket visas den i text rutan omvandling för att dokumentera dina omvandlings uttryck:

![Kommentarer](media/data-flow/comments2.png "Kommentarer")

## <a name="convert-to-dates-or-timestamps"></a>Konvertera till datum eller tidsstämplar

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Observera att om du vill inkludera sträng litteraler i dina tidsstämplar måste du omsluta din konvertering i en toString ()

## <a name="handling-column-names-with-special-characters"></a>Hantera kolumn namn med specialtecken

När du har kolumn namn som innehåller specialtecken eller mellanslag, omger du namnet med klammerparenteser.

```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Nästa steg

[Börja bygga data omvandlings uttryck](data-flow-expression-functions.md)
