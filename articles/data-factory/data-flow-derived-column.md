---
title: Härledd kolumnomvandling vid mappning av dataflöde
description: Lär dig hur du omvandlar data i stor skala i Azure Data Factory med omvandlingen av dataflödet för mappningsdataflöde.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 06cb868071612ae2825e86cac32734dcd279c99f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413850"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Härledd kolumnomvandling vid mappning av dataflöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Använd den härledda kolumnomvandlingen för att generera nya kolumner i dataflödet eller för att ändra befintliga fält.

## <a name="derived-column-settings"></a>Härledda kolumninställningar

Om du vill åsidosätta en befintlig kolumn markerar du den via kolumnrullgardermenyn. Annars använder du kolumnmarkeringsfältet som en textruta och skriver in den nya kolumnens namn. Om du vill skapa den härledda kolumnens uttryck klickar du på rutan "Ange uttryck" för att öppna [dataflödesuttrycksverktyget](concepts-data-flow-expression-builder.md).

![Härledda kolumninställningar](media/data-flow/dc1.png "Härledda kolumninställningar")

Om du vill lägga till ytterligare härledda kolumner hovrar du över en befintlig härledd kolumn och klickar på plusikonen. Välj antingen **Lägg till kolumn** eller Lägg till **kolumnmönster**. Kolumnmönster kan vara till pass om kolumnnamnen är variabla från dina källor. Mer information finns i [Kolumnmönster](concepts-data-flow-column-pattern.md).

![Ny härledd kolumnmarkering](media/data-flow/columnpattern.png "Ny härledd kolumnmarkering")

## <a name="build-schemas-in-output-schema-pane"></a>Skapa scheman i fönstret Utdataschema

Kolumnerna som du ändrar och lägger till i schemat visas i fönstret Utdataschema. Du kan interaktivt skapa enkla och komplexa datastrukturer här. Om du vill lägga till ytterligare fält väljer du **Lägg till kolumn**. Om du vill skapa hierarkier väljer du **Lägg till underkolunn**.

![Lägg till underkolumn](media/data-flow/addsubcolumn.png "Lägg till underkolumn")

Mer information om hur du hanterar komplexa typer i dataflödet finns [i JSON-hantering vid mappning av dataflöde](format-json.md#mapping-data-flow-properties).

![Lägga till komplex kolumn](media/data-flow/complexcolumn.png "Lägg till kolumner")

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

Exemplet nedan är en härledd kolumn med namnet `CleanData` som tar en inkommande ström `MoviesYear` och skapar två härledda kolumner. Den första härledda `Rating` kolumnen ersätter kolumnen med Rating värde som en heltalstyp. Den andra härledda kolumnen är ett mönster som matchar varje kolumn vars namn börjar med "filmer". För varje matchad kolumn skapas `movie` en kolumn som är lika med värdet för den matchade kolumnen som föregås av "movie_". 

I Data Factory UX ser den här omvandlingen ut som bilden nedan:

![Härleda exempel](media/data-flow/derive-script1.png "Härleda exempel")

Dataflödesskriptet för den här omvandlingen finns i kodavsnittet nedan:

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

- Läs mer om [uttrycksspråket Mappa dataflöde](data-flow-expression-functions.md).
