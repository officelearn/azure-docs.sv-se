---
title: Filtrera omvandling vid mappning av dataflöde
description: Filtrera bort rader med hjälp av filteromvandlingen i Azure Data Factory-mappningsdataflödet
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413734"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtrera omvandling vid mappning av dataflöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Filtertransformeringar tillåter radfiltrering baserat på ett villkor. Utdataströmmen innehåller alla rader som matchar filtreringsvillkoret. Filteromvandlingen liknar en WHERE-sats i SQL.

## <a name="configuration"></a>Konfiguration

Använd dataflödesuttrycksverktyget för att ange ett uttryck för filtervillkoret. Om du vill öppna uttrycksverktyget klickar du på den blå rutan. Filtervillkoret måste vara av typen boolesk. Mer information om hur du skapar ett uttryck finns i dokumentationen till [uttrycksverktyget.](concepts-data-flow-expression-builder.md)

![Filtrera omformning](media/data-flow/filter1.png "Filtrera omformning")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en `FilterBefore1960` filteromvandling `CleanData`som visas som tar i inkommande ström . Filtervillkoret är `year <= 1960`uttrycket .

I Data Factory UX ser den här omvandlingen ut som bilden nedan:

![Filtrera omformning](media/data-flow/filter1.png "Filtrera omformning")

Dataflödesskriptet för den här omvandlingen finns i kodavsnittet nedan:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Nästa steg

Filtrera bort kolumner med [markeringsomvandlingen](data-flow-select.md)
