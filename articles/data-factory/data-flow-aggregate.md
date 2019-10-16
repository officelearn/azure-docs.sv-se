---
title: Sammanställd transformering i data flöde för mappning – Azure Data Factory | Microsoft Docs
description: Lär dig hur du sammanställer data i skala i Azure Data Factory med den sammanställda omvandlingen för data flöde.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 1dcc28313d1d8e59024fbc70738567cb59585d20
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326465"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Sammanställd transformering i mappnings data flödet 



Med den sammanställda omvandlingen kan du definiera agg regeringar för kolumner i dina data strömmar. Med uttrycks verktyget kan du definiera olika typer av agg regeringar som SUM, MIN, MAX och COUNT som kan grupperas efter befintliga eller beräknade kolumner.

## <a name="group-by"></a>Gruppera efter
Välj en befintlig kolumn eller skapa en ny beräknad kolumn som ska användas som en Group by-sats för din AGG regering. Om du vill använda en befintlig kolumn väljer du önskad kolumn i list rutan. Om du vill skapa en ny beräknad kolumn hovrar du över satsen och klickar på beräknad kolumn. Detta öppnar [uttrycks verktyget för data flöde](concepts-data-flow-expression-builder.md). När du har skapat en beräknad kolumn anger du namnet på kolumnen utdata i fältet "namn som". Om du vill lägga till ytterligare en Group by-sats, Hovra över en befintlig sats och klicka på +.

![Sammanställd transformerings grupp efter inställningar](media/data-flow/agg.png "sammanställd omvandlings grupp efter inställningar")

> [!NOTE]
> En Group by-sats är valfri i en mängd omvandling.

## <a name="aggregate-column"></a>Sammanställd kolumn 
Välj fliken aggregator för att bygga agg regerings uttryck. Du kan antingen välja en befintlig kolumn och skriva över värdet med agg regeringen, eller skapa ett nytt fält med ett nytt namn. Agg regerings uttrycket anges i den högra rutan bredvid kolumn namns väljaren. Redigera uttrycket genom att klicka på text rutan för att öppna uttrycks verktyget. Om du vill lägga till ytterligare en agg regering hovrar du över ett befintligt uttryck och klickar på + för att skapa ett nytt agg regerings kolumn-eller [kolumn mönster](concepts-data-flow-column-pattern.md).

Sammanställda(media/data-flow/agg2.png "mängd inställningar för") sammanställd ![transformering]

> [!NOTE]
> Varje agg regerings uttryck måste innehålla minst en mängd funktion.

> [!NOTE]
> I fel söknings läge kan uttrycks verktyget inte producera data för hands versionerna med mängd funktioner. Om du vill visa data förhands granskningar för sammanställda transformeringar stänger du uttrycks verktyget och visar data via fliken Data förhands granskning.

## <a name="reconnect-rows-and-columns"></a>Återanslut rader och kolumner
Sammanställda transformeringar motsvarar i nära SQL agg regerings urvals frågor. Kolumner som inte ingår i din Group by-sats eller mängd funktioner flödar inte genom utdata från din mängd omvandling. Om det finns andra kolumner som du vill inkludera i dina sammanställda rader, måste du antingen:

* Använd en mängd funktion för att inkludera ytterligare en kolumn, till exempel Last () eller First ()
* Koppla ihop kolumnerna igen innan du börjar samla in med hjälp av [själv kopplings mönstret](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Skript för data flöde

Samlings(media/data-flow/aggdfs1.png "skript") för sammanställd ![data flödes skript]

* ```MoviesYear```: härledd kolumn som definierar år och rubrik kolumner
* ```AvgComedyRatingByYear```: aggregerad omvandling för genomsnittlig bedömning av Comedies grupperat per år
* ```avgrating```: namnet på den nya kolumnen som skapas för att innehålla det sammanställda värdet

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```
  
## <a name="next-steps"></a>Nästa steg

* Definiera Window-baserad agg regering med [fönster omvandlingen](data-flow-window.md)
