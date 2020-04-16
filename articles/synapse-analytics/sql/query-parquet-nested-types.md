---
title: Frågeparkettkapslade typer med SQL på begäran (förhandsgranskning)
description: I den här artikeln får du lära dig att fråga parett kapslade typer.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431662"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Frågeparkettkapslade typer med SQL on-demand (förhandsversion) i Azure Synapse Analytics

I den här artikeln får du lära dig hur du skriver en fråga med SQL on-demand (preview) i Azure Synapse Analytics.  Den här frågan kommer att läsa Parquet kapslade typer.

## <a name="prerequisites"></a>Krav

Innan du läser resten av den här artikeln, granska följande artiklar:

- [Första gången setup](query-data-storage.md#first-time-setup)
- [Krav](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Projekt kapslade eller upprepade data

Följande fråga läser *justSimpleArray.parkquet* filen. Den projicerar alla kolumner från parettfilen, inklusive kapslade eller upprepade data.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Komma åt element från kapslade kolumner

Följande fråga läser *filen structExample.parkquet* och visar hur du visar hur du visar hur element i en kapslad kolumn ska visas:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Komma åt element från upprepade kolumner

Följande fråga läser *filen justSimpleArray.parkquet* och använder [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att hämta ett **skalärelement** inifrån en upprepad kolumn, till exempel en array eller karta:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

Följande fråga läser *filen mapExample.parkquet* och använder [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att hämta ett **icke-skalärelement** inifrån en upprepad kolumn, till exempel en array eller karta:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Nästa steg

Nästa artikel visar hur du [frågar JSON-filer](query-json-files.md).
