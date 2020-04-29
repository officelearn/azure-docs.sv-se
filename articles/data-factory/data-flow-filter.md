---
title: Filter omvandling i data flöde för mappning
description: Filtrera bort rader med hjälp av filter omvandlingen i Azure Data Factory mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606426"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filter omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Filtret Transforms tillåter rad filtrering baserat på ett villkor. Utdataströmmen innehåller alla rader som matchar filtrerings villkoret. Filter omvandlingen liknar en WHERE-sats i SQL.

## <a name="configuration"></a>Konfiguration

Använd data flödets uttrycks verktyg för att ange ett uttryck för filter villkoret. Öppna uttrycks verktyget genom att klicka på den blå rutan. Filter villkoret måste vara av typen Boolean. Mer information om hur du skapar ett uttryck finns i [uttrycks verktygets](concepts-data-flow-expression-builder.md) dokumentation.

![Filtrera omvandling](media/data-flow/filter1.png "Filtrera omvandling")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en filter omvandling med namnet `FilterBefore1960` som tar i inkommande data `CleanData`ström. Filter villkoret är uttrycket `year <= 1960`.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Filtrera omvandling](media/data-flow/filter1.png "Filtrera omvandling")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Nästa steg

Filtrera ut kolumner med [Select-omvandlingen](data-flow-select.md)
