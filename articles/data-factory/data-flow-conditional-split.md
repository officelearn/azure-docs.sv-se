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
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606487"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Villkorlig delnings omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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

Exemplet nedan är en villkorlig delad omvandling med namnet `SplitByYear` som tar i inkommande data `CleanData`ström. Den här omvandlingen har två delade `year < 1960` villkor `year > 1980`och. `disjoint`är falskt eftersom data hamnar i det första matchnings villkoret. Varje rad som matchar det första villkoret går till `moviesBefore1960`utdataströmmen. Alla återstående rader som matchar det andra villkoret går till `moviesAFter1980`utdataström. Alla andra rader flödar genom standard data `AllOtherMovies`strömmen.

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
