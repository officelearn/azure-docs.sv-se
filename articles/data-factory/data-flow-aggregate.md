---
title: Aggregerad omvandling i mappning av dataflöde
description: Lär dig hur du aggregerade data i skala i Azure Data Factory med mappningsdataflödet Aggregerad omvandling.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 050fcc33f0e3d65ca8e94b3be143c5b1ae77608d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416559"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Aggregerad omvandling i mappning av dataflöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den aggregerade omvandlingen definierar aggregeringar av kolumner i dina dataströmmar. Med uttrycksverktyget kan du definiera olika typer av aggregeringar, till exempel SUMMA, MIN, MAX och COUNT grupperade efter befintliga eller beräknade kolumner.

## <a name="group-by"></a>Gruppera efter

Markera en befintlig kolumn eller skapa en ny beräknad kolumn som ska användas som grupp för sats för aggregering. Om du vill använda en befintlig kolumn markerar du den i listrutan. Om du vill skapa en ny beräknad kolumn håller du muspekaren över satsen och klickar på **Beräknad kolumn**. Då öppnas [dataflödesuttrycksverktyget](concepts-data-flow-expression-builder.md). När du har skapat den beräknade kolumnen anger du utdatakolumnnamnet under fältet **Namn som.** Om du vill lägga till ytterligare en grupp för sats håller du muspekaren över en befintlig sats och klickar på plusikonen.

![Samla omvandlingsgrupp efter inställningar](media/data-flow/agg.png "Samla omvandlingsgrupp efter inställningar")

En grupp för sats är valfri i en mängdomvandling.

## <a name="aggregate-column"></a>Aggregerad kolumn 

Gå till fliken **Aggregat** för att skapa aggregeringsuttryck. Du kan antingen skriva över en befintlig kolumn med en aggregering eller skapa ett nytt fält med ett nytt namn. Aggregeringsuttrycket anges i den högra rutan bredvid kolumnnamnväljaren. Om du vill redigera uttrycket klickar du på textrutan för att öppna uttrycksverktyget. Om du vill lägga till ytterligare aggregeringar håller du muspekaren över ett befintligt uttryck och klickar på plusikonen för att skapa en ny aggregeringskolumn eller [kolumnmönster](concepts-data-flow-column-pattern.md).

Varje aggregeringsuttryck måste innehålla minst en mängdfunktion.

![Sammanlagda inställningar för mängd av aggregation](media/data-flow/agg2.png "Sammanlagda inställningar för mängd av aggregation")


> [!NOTE]
> I felsökningsläge kan uttrycksverktyget inte skapa dataförhandsgranskningar med mängdfunktioner. Om du vill visa förhandsgranskningar av data för aggregerade omvandlingar stänger du uttrycksverktyget och visar data via fliken Dataförhandsgranskning.

## <a name="reconnect-rows-and-columns"></a>Återansluta rader och kolumner

Aggregerade omvandlingar liknar SQL-mängdvalsfrågor. Kolumner som inte ingår i gruppen efter sats eller aggregerade funktioner flödar inte fram till utdata från den sammanlagda omvandlingen. Om du vill ta med andra kolumner i den aggregerade utdata gör du någon av följande metoder:

* Använd en `last()` mängdfunktion, `first()` till exempel eller för att inkludera den ytterligare kolumnen.
* Gå tillbaka till kolumnerna till utdataströmmen med hjälp av [självkopplingsmönstret](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Ta bort dubblettrader

En vanlig användning av den sammanlagda omvandlingen är att ta bort eller identifiera dubblettposter i källdata. Den här processen kallas deduplicering. Baserat på en uppsättning grupper efter nycklar använder du en heuristisk som du väljer för att avgöra vilken dubblettrad du ska behålla. Vanliga heuristik `first()` `last()`är `max()`, `min()`, och . Använd [kolumnmönster](concepts-data-flow-column-pattern.md) för att tillämpa regeln på alla kolumner utom gruppen efter kolumner.

![Deduplicering](media/data-flow/agg-dedupe.png "Deduplicering")

I exemplet ovan `ProductID` används `Name` kolumner och används för gruppering. Om två rader har samma värden för dessa två kolumner betraktas de som dubbletter. I den här sammanlagda omvandlingen behålls värdena för den första raden som matchas och alla andra tas bort. Med hjälp av syntax för kolumnmönster, alla kolumner vars namn inte `ProductID` är och `Name` mappas till deras befintliga kolumnnamn och ges värdet på de första matchade raderna. Utdataschemat är detsamma som indataschemat.

För scenarier för `count()` dataverifiering kan funktionen användas för att räkna hur många dubbletter det finns.

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

I exemplet nedan visas `MoviesYear` en inkommande ström `year`och grupperar rader efter kolumn . Omvandlingen skapar en `avgrating` mängdkolumn som utvärderas till medelvärdet av kolumnen `Rating`. Den här sammanlagda omvandlingen heter `AvgComedyRatingsByYear`.

I Data Factory UX ser den här omvandlingen ut som bilden nedan:

![Grupp efter exempel](media/data-flow/agg-script1.png "Grupp efter exempel")

![Aggregerat exempel](media/data-flow/agg-script2.png "Aggregerat exempel")

Dataflödesskriptet för den här omvandlingen finns i kodavsnittet nedan.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Aggregerat dataflödesskript](media/data-flow/aggdfs1.png "Aggregerat dataflödesskript")

```MoviesYear```: Härledd kolumn definiera ```AvgComedyRatingByYear```år och titel kolumner: Aggregerad ```avgrating```omvandling för genomsnittlig rating av komedier grupperade efter år: Namn på ny kolumn som skapas för att hålla det aggregerade värdet

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Nästa steg

* Definiera fönsterbaserad aggregering med [windows-omvandlingen](data-flow-window.md)
