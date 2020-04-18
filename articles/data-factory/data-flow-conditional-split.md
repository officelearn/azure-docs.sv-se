---
title: Villkorlig delningsomvandling i mappning av dataflöde
description: Dela upp data i olika strömmar med hjälp av den villkorliga delningsomvandlingen i Azure Data Factory-mappningsdataflödet
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606487"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Villkorlig delningsomvandling i mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den villkorliga delade omvandlingen dirigerar datarader till olika strömmar baserat på matchande villkor. Den villkorliga delningsomvandlingen liknar en CASE-beslutsstruktur i ett programmeringsspråk. Omvandlingen utvärderar uttryck och baseras på resultaten dirigerar dataraden till den angivna strömmen.

## <a name="configuration"></a>Konfiguration

Inställningen **Dela på** avgör om raden med data flödar till den första matchande strömmen eller varje ström som den matchar till.

Använd dataflödesuttrycksverktyget för att ange ett uttryck för delningsvillkoret. Om du vill lägga till ett nytt villkor klickar du på plusikonen i en befintlig rad. En standardström kan också läggas till för rader som inte matchar något villkor.

![villkorlig delning](media/data-flow/conditionalsplit1.png "villkorsstyrda delningsalternativ")

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

Exemplet nedan är en villkorlig `SplitByYear` delningsomvandling som visas som tar i inkommande ström `CleanData`. Denna omvandling har `year < 1960` två `year > 1980`delade villkor och . `disjoint`är falskt eftersom data går till det första matchande villkoret. Varje rad som matchar det `moviesBefore1960`första villkoret går till utdataströmmen . Alla återstående rader som matchar det `moviesAFter1980`andra villkoret går till utdataströmmen . Alla andra rader flödar `AllOtherMovies`genom standardströmmen .

I Data Factory UX ser den här omvandlingen ut som bilden nedan:

![villkorlig delning](media/data-flow/conditionalsplit1.png "villkorsstyrda delningsalternativ")

Dataflödesskriptet för den här omvandlingen finns i kodavsnittet nedan:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Nästa steg

Vanliga dataflödesomvandlingar som används med villkorsstyrd delning är [kopplingsomvandling,](data-flow-join.md) [uppslagsomvandling](data-flow-lookup.md)och [välja omformning](data-flow-select.md)
