---
title: Härledd kolumn omvandling i data flöde för mappning
description: Lär dig hur du omvandlar data i skala i Azure Data Factory med den härledda kolumn omvandlingen för data flöde.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: c2d4c9b089a6673a5cff716d2901cc2b9483ede1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972259"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Härledd kolumn omvandling i data flöde för mappning

Använd omvandlingen för härledd kolumn för att skapa nya kolumner i ditt data flöde eller ändra befintliga fält.

## <a name="derived-column-settings"></a>Härledda kolumn inställningar

Om du vill åsidosätta en befintlig kolumn väljer du den via List rutan kolumn. Annars använder du fältet kolumn markering som en text ruta och skriver in namnet på den nya kolumnen. Skapa uttrycket för den härledda kolumnen genom att klicka på rutan "Ange uttryck" för att öppna [uttrycks verktyget för data flödet](concepts-data-flow-expression-builder.md).

![Härledda kolumn inställningar](media/data-flow/dc1.png "Härledda kolumn inställningar")

Om du vill lägga till ytterligare härledda kolumner, Hovra över en befintlig härledd kolumn och klicka på plus ikonen. Välj antingen **Lägg till kolumn** eller **Lägg till kolumn mönster**. Kolumn mönster kan komma att vara praktiska om kolumn namnen är variabla från dina källor. Mer information finns i [kolumn mönster](concepts-data-flow-column-pattern.md).

![Ny härledd kolumn markering](media/data-flow/columnpattern.png "Ny härledd kolumn markering")

## <a name="build-schemas-in-output-schema-pane"></a>Bygg scheman i fönstret utdata schema

De kolumner som du ändrar och lägger till i schemat visas i fönstret utdata schema. Du kan bygga enkla och komplexa data strukturer interaktivt här. Om du vill lägga till fler fält väljer du **Lägg till kolumn**. Välj **Lägg till under kolumn**för att skapa hierarkier.

![Lägg till under kolumn](media/data-flow/addsubcolumn.png "Lägg till under kolumn")

Mer information om hur du hanterar komplexa typer i data flöde finns i [JSON-hantering i mappa data flöde](concepts-data-flow-json.md)

![Lägg till komplex kolumn](media/data-flow/complexcolumn.png "Lägga till kolumner")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en härledd kolumn med namnet `CleanData` som tar en inkommande data ström `MoviesYear` och skapar två härledda kolumner. Den första härledda kolumnen ersätter kolumnen `Rating` med gradering svärdet som en heltals typ. Den andra härledda kolumnen är ett mönster som matchar varje kolumn vars namn börjar med "filmer". För varje matchad kolumn skapar den en kolumn `movie` som är lika med värdet för den matchade kolumnen som föregås av movie_. 

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Härled exempel](media/data-flow/derive-script1.png "Härled exempel")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [språket för mappning av data Flow-uttryck](data-flow-expression-functions.md).
