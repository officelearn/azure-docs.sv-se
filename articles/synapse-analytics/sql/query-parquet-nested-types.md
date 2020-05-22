---
title: Fråga Parquet kapslade typer med SQL på begäran (för hands version)
description: I den här artikeln får du lära dig hur du frågar Parquet-kapslade typer.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 82edee84317b5d542bf65e29514286f96c18bbcc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744225"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Fråga Parquet kapslade typer med SQL on-demand (för hands version) i Azure Synapse Analytics

I den här artikeln får du lära dig hur du skriver en fråga med SQL på begäran (för hands version) i Azure Synapse Analytics.  Den här frågan läser Parquet-kapslade typer.

## <a name="prerequisites"></a>Krav

Ditt första steg är att **skapa en databas** med en data källa som refererar till. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används i de här exemplen.

## <a name="project-nested-or-repeated-data"></a>Projekt kapslade eller upprepade data

Följande fråga läser filen *justSimpleArray. Parquet* . Den projekterar alla kolumner från Parquet-filen, inklusive kapslade eller upprepade data.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Få åtkomst till element från kapslade kolumner

Följande fråga läser filen *structExample. Parquet* och visar hur du delar upp element i en kapslad kolumn:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

## <a name="access-elements-from-repeated-columns"></a>Få åtkomst till element från upprepade kolumner

Följande fråga läser filen *justSimpleArray. Parquet* och använder [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att hämta ett **skalärt** element i en upprepad kolumn, t. ex. en matris eller karta:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Följande fråga läser filen *mapExample. Parquet* och använder [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att hämta ett **icke-skalärt** element i en upprepad kolumn, t. ex. en matris eller karta:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Nästa steg

I nästa artikel visas hur du [frågar JSON-filer](query-json-files.md).
