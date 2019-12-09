---
title: Villkorlig delnings omvandling i data flöde för mappning
description: Dela data i olika strömmar med den villkorliga delnings omvandlingen i Azure Data Factory mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: d7e2af6c98951e685192656b37226716e4340bfe
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930437"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Villkorlig delnings omvandling i data flöde för mappning

Den villkorliga delnings Omvandlingen dirigerar data rader till olika strömmar baserat på matchnings villkor. Den villkorliga delnings omvandlingen liknar en ärende besluts struktur i ett programmeringsspråk. Omvandlingen utvärderar uttryck och baseras på resultaten och dirigerar data raden till den angivna data strömmen.

## <a name="configuration"></a>Konfiguration

Inställningen **dela vid** bestämmer om raden med data flödar till den första matchande data strömmen eller varje data ström som den matchar.

Använd data flödets uttrycks verktyg för att ange ett uttryck för det delade villkoret. Om du vill lägga till ett nytt villkor klickar du på plus ikonen i en befintlig rad. Du kan även lägga till en standard data ström för rader som inte matchar något villkor.

![villkorlig delning](media/data-flow/conditionalsplit1.png "alternativ för villkorlig delning")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Exempel

Exemplet nedan är en villkorlig delad omvandling med namnet `SplitByYear` som tar emot inkommande data strömmar `CleanData`. Den här omvandlingen har två delnings villkor `year < 1960` och `year > 1980`. `disjoint` är falskt eftersom data hamnar i det första matchnings villkoret. Varje rad som matchar det första villkoret går till utdata Stream `moviesBefore1960`. Alla återstående rader som matchar det andra villkoret går till utdataström `moviesAFter1980`. Alla andra rader flödar genom standard data strömmen `AllOtherMovies`.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![villkorlig delning](media/data-flow/conditionalsplit1.png "alternativ för villkorlig delning")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Nästa steg

Vanliga data flödes omvandlingar som används med villkorlig delning är [Join-omvandling](data-flow-join.md), [Lookup-omvandling](data-flow-lookup.md)och [Välj omvandling](data-flow-select.md)
