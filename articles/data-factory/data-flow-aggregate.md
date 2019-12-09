---
title: Sammanställd transformering i mappnings data flödet
description: Lär dig hur du sammanställer data i skala i Azure Data Factory med den sammanställda omvandlingen för data flöde.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 74b96bf2cac0de7c57e496c637f2e3ef549eb61f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930460"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Sammanställd transformering i mappnings data flödet 

Den sammanställda omvandlingen definierar agg regeringar för kolumner i dina data strömmar. Med uttrycks verktyget kan du definiera olika typer av agg regeringar som SUM, MIN, MAX och COUNT grupperade efter befintliga eller beräknade kolumner.

## <a name="group-by"></a>Gruppera efter

Välj en befintlig kolumn eller skapa en ny beräknad kolumn som ska användas som en Group by-sats för din AGG regering. Om du vill använda en befintlig kolumn väljer du den i list rutan. För att skapa en ny beräknad kolumn, Hovra över satsen och klicka på **beräknad kolumn**. Detta öppnar [uttrycks verktyget för data flöde](concepts-data-flow-expression-builder.md). När du har skapat en beräknad kolumn anger du namnet på kolumnen utdata i fältet **namn som** . Om du vill lägga till ytterligare en Group by-sats, Hovra över en befintlig sats och klicka på plus-ikonen.

![Sammanställd omvandlings grupp efter inställningar](media/data-flow/agg.png "Sammanställd omvandlings grupp efter inställningar")

En Group by-sats är valfri i en mängd omvandling.

## <a name="aggregate-column"></a>Sammanställd kolumn 

Gå till fliken **Aggregator** för att bygga agg regerings uttryck. Du kan antingen skriva över en befintlig kolumn med en agg regering eller skapa ett nytt fält med ett nytt namn. Agg regerings uttrycket anges i den högra rutan bredvid kolumn namns väljaren. Redigera uttrycket genom att klicka på text rutan för att öppna uttrycks verktyget. Om du vill lägga till fler agg regeringar hovrar du över ett befintligt uttryck och klickar på plus ikonen för att skapa ett nytt agg regerings kolumn-eller [kolumn mönster](concepts-data-flow-column-pattern.md).

Varje agg regerings uttryck måste innehålla minst en mängd funktion.

![Mängd inställningar för sammanställd transformering](media/data-flow/agg2.png "Mängd inställningar för sammanställd transformering")


> [!NOTE]
> I fel söknings läge kan uttrycks verktyget inte producera data för hands versionerna med mängd funktioner. Om du vill visa data förhands granskningar för sammanställda transformeringar stänger du uttrycks verktyget och visar data via fliken Data förhands granskning.

## <a name="reconnect-rows-and-columns"></a>Återanslut rader och kolumner

Sammanställda transformeringar liknar SQL-aggregerade urvals frågor. Kolumner som inte ingår i din Group by-sats eller mängd funktioner flödar inte genom utdata från din mängd omvandling. Om du vill inkludera andra kolumner i dina aggregerade utdata gör du något av följande:

* Använd en mängd funktion som `last()` eller `first()` för att inkludera den ytterligare kolumnen.
* Återanslut kolumnerna till utdataström med hjälp av [själv kopplings mönstret](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Exempel

I exemplet nedan tar en inkommande data ström `MoviesYear` och grupperas rader efter kolumn `year`. Omvandlingen skapar en sammanställd kolumn `avgrating` som utvärderas till genomsnittet av kolumn `Rating`. Den sammanställda omvandlingen heter `AvgComedyRatingsByYear`.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Gruppera efter exempel](media/data-flow/agg-script1.png "Gruppera efter exempel")

![Samlings exempel](media/data-flow/agg-script2.png "Samlings exempel")

Data flödes skriptet för den här omvandlingen finns i kodfragmentet nedan.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Nästa steg

* Definiera Window-baserad agg regering med [fönster omvandlingen](data-flow-window.md)
