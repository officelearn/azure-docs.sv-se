---
title: Azure Data Factory mappar uttrycks verktyg för data flöde
description: Uttrycks verktyget för Azure Data Factory mappning av data flöden
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9862866d5cddb227d9417ac15db6b8ea851507e6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030290"
---
# <a name="mapping-data-flow-expression-builder"></a>Mappa data flöde uttrycks verktyg



I Azure Data Factory mappa data flödet hittar du resultat rutor där du kan ange uttryck för data omvandling. Använd kolumner, fält, variabler, parametrar, funktioner från ditt data flöde i dessa rutor. Om du vill bygga uttrycket använder du uttrycks verktyget, som startas genom att klicka i text rutan uttryck inuti omvandlingen. Ibland kan du också se alternativen för beräknad kolumn när du väljer kolumner för omvandling. När du klickar på den visas även uttrycks verktyget.

![Uttrycks verktyg för uttrycks verktyget](media/data-flow/xpb1.png "")

Uttrycks verktyget standard är standard alternativet text redigerare. funktionen för automatisk komplettering läser från hela Azure Data Factory data flödes objekt modell med syntaxkontroll och markering.

![Uttrycks]automatisk komplettering i uttrycks verktyg(media/data-flow/expb1.png "automatiskt")

## <a name="build-schemas-in-output-schema-pane"></a>Bygg scheman i fönstret utdata schema

![Lägg till komplexa]kolumner(media/data-flow/complexcolumn.png "Lägg till kolumner")

I fönstret till vänster schema för utdata visas de kolumner som du ändrar och lägger till i schemat. Du kan bygga enkla och komplexa data strukturer interaktivt här. Lägg till ytterligare fält med hjälp av "Lägg till kolumn" och skapa hierarkier med "Lägg till underkolumn".

![Lägg]till under kolumn(media/data-flow/addsubcolumn.png "Lägg till under kolumn")

## <a name="data-preview-in-debug-mode"></a>Förhandsgranska data i fel söknings läge

(media/data-flow/exp4b.png "Förhands granskning") av ![uttrycks]data

När du arbetar med dina data flödes uttryck växlar du till fel söknings läge från den Azure Data Factory data flödets design yta, vilket möjliggör en pågående för hands version av data resultaten från det uttryck som du skapar. Direkt fel sökning i real tid har Aktiver ATS för dina uttryck.

(media/data-flow/debugbutton.png "Knappen Felsök") i ![fel söknings läge]

Klicka på Uppdatera om du vill uppdatera resultatet av ditt uttryck mot ett Live-exempel på källan i real tid.

(media/data-flow/exp5.png "Förhands granskning") av ![uttrycks]data

## <a name="comments"></a>Kommentar

Lägg till kommentarer till dina uttryck med hjälp av en enkel rad och en kommentar med flera rader:

![Kommentarer](media/data-flow/comments.png "")

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

(media/data-flow/expb2.png "Förhands granskning") av Expression ![Builder mat ris]-data

## <a name="handling-names-with-special-characters"></a>Hantera namn med specialtecken

När du har kolumn namn som innehåller specialtecken eller mellanslag, omger du namnet med klammerparenteser.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Nästa steg

[Börja bygga data omvandlings uttryck](data-flow-expression-functions.md)
