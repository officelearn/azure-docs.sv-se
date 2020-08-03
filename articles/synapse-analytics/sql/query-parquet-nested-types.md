---
title: Fråga Parquet kapslade typer med SQL på begäran (för hands version)
description: I den här artikeln får du lära dig hur du frågar Parquet-kapslade typer.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: fb56c4da77ddeb87ebc3724a3b138994e4da98e7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489698"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Fråga efter kapslade typer i Parquet-och JSON-filer med SQL on-demand (för hands version) i Azure Synapse Analytics

I den här artikeln får du lära dig hur du skriver en fråga med SQL på begäran (för hands version) i Azure Synapse Analytics. Den här frågan läser Parquet-kapslade typer.
Kapslade typer är komplexa strukturer som representerar objekt eller matriser. Kapslade typer kan lagras i: 
- [PARQUET](query-parquet-files.md) där du kan ha flera komplexa kolumner som innehåller matriser och objekt.
- Hierarkiska [JSON-filer](query-json-files.md) där du kan läsa komplexa JSON-dokument som en enda kolumn.
- CosmosDB-samling där varje dokument kan innehålla komplexa kapslade egenskaper (för närvarande för närvarande en offentlig för hands version).

Synapse SQL på begäran formaterar alla kapslade typer som JSON-objekt och matriser, så att du kan [extrahera eller ändra komplexa objekt med hjälp av JSON-funktioner](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) eller [parsa JSON-data med hjälp av openjson-funktionen](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Ett exempel på en fråga som extraherar skalära värden och objekt värden från [COVID-19 Open Research data uppsättning](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON-filen med kapslade objekt visas nedan. 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`funktionen returnerar ett skalärt värde från fältet vid den angivna sökvägen. `JSON_QUERY`funktionen returnerar ett objekt formaterat som JSON från fältet vid den angivna sökvägen.

> [!IMPORTANT]
> I det här exemplet används en fil från [COVID-19 Open Research data uppsättning](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Se den här-licensen och data strukturen på den här sidan.

## <a name="prerequisites"></a>Förutsättningar

Ditt första steg är att **skapa en databas** med en data källa som refererar till. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används i de här exemplen.

## <a name="project-nested-or-repeated-data"></a>Projekt kapslade eller upprepade data

PARQUET-filen kan ha flera kolumner med komplexa typer. Värdena från dessa kolumner är formaterade som JSON-text och returneras som VARCHAR-kolumn. Följande fråga läser filen *structExample. Parquet* och visar hur du läser värdena för de kapslade kolumnerna: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Den här frågan returnerar följande resultat där innehållet för varje kapslat objekt returneras som JSON-text:

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Datum": "2009-04-25"}| {"Tid": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0,5}|
|{"Datum": "1916-04-29"}| {"Tid": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

Följande fråga läser filen *justSimpleArray. Parquet* . Den projekterar alla kolumner från Parquet-filen, inklusive kapslade eller upprepade data.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Den här frågan returnerar följande resultat:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Läsa egenskaper från kolumner med kapslade objekt

`JSON_VALUE`funktionen gör att du kan returnera värden från kolumnen formaterad som JSON-text:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Resultatet visas i följande tabell:

|rubrik  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Extra information ett eko-epidemiolo... | Julien   | – Bild S1: Phylogeny av... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Till skillnad från JSON-filer som i de flesta fall returnerar en enskild kolumn som innehåller komplexa JSON-objekt. PARQUET-filer kan ha flera komplexa. Du kan läsa egenskaperna för kapslad kolumn med `JSON_VALUE` funktionen i varje kolumn. `OPENROWSET`gör att du kan ange sökvägar för kapslade egenskaper i- `WITH` satsen direkt. Sökvägar kan anges som ett namn på kolumnen eller så kan du lägga till ett [JSON-sökuttryck](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) efter kolumn typ.

Följande fråga läser filen *structExample. Parquet* och visar hur man Surface-element i en kapslad kolumn. Det finns två sätt att referera till kapslat värde:
- Ange det kapslade värdet Path-uttrycket efter typ specifikationen.
- Formatera kolumn namnet som en kapslad sökväg med hjälp av "." för att referera till fälten.

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
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
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

Resultatet visas i följande tabell:

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Komma åt underordnade objekt från komplexa kolumner

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

Du kan också uttryckligen referera till de kolumner som du vill returnera i- `WITH` satsen:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

Strukturen `MapOfPersons` returneras som en `VARCHAR` kolumn och formateras som JSON-sträng.

## <a name="project-values-from-repeated-columns"></a>Projicera värden från upprepade kolumner

Om du har en matris med skalära värden (till exempel `[1,2,3]` ) i vissa kolumner kan du enkelt expandera dem och koppla dem till huvud raden med hjälp av följande skript:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Nästa steg

I nästa artikel visas hur du [frågar JSON-filer](query-json-files.md).
