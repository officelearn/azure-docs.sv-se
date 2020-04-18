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
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606426"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtrera omvandling vid mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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
