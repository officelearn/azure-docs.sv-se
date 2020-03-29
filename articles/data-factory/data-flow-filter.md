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
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930356"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtrera omvandling vid mappning av dataflöde

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
