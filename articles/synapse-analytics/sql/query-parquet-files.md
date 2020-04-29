---
title: Fråga Parquet-filer med SQL på begäran (för hands version)
description: I den här artikeln får du lära dig hur du frågar Parquet-filer med SQL på begäran (för hands version).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431701"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Fråga Parquet-filer med SQL on-demand (för hands version) i Azure Synapse Analytics

I den här artikeln får du lära dig hur du skriver en fråga med SQL på begäran (för hands version) som kommer att läsa Parquet-filer.

## <a name="prerequisites"></a>Krav

Läs följande artiklar innan du läser resten av den här artikeln:

- [Installation vid första tiden](query-data-storage.md#first-time-setup)
- [Förutsättningar](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Datauppsättning

Du kan fråga Parquet-filer på samma sätt som du läser CSV-filer. Den enda skillnaden är att parametern FILEFORMAT ska anges till PARQUET. Exemplen i den här artikeln visar information om hur du läser Parquet-filer.

> [!NOTE]
> Du behöver inte ange kolumner i OpenRowSet WITH-satsen när du läser Parquet-filer. SQL på begäran kommer att använda metadata i Parquet-filen och binda kolumner efter namn.

Du använder mappen *Parquet/taxi* för exempel frågorna. Den innehåller NYC taxi-Yellow taxi-resa registrerar data från 2016 juli. till juni 2018.

Data partitioneras per år och månad och mappstrukturen är följande:

- år = 2016
  - månad = 6
  - ...
  - månad = 12
- år = 2017
  - månad = 1
  - ...
  - månad = 12
- år = 2018
  - månad = 1
  - ...
  - månad = 6

## <a name="query-set-of-parquet-files"></a>Fråga uppsättning Parquet-filer

Du kan bara ange intressanta kolumner när du frågar Parquet-filer.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Automatisk schema härledning

Du behöver inte använda OpenRowSet WITH-satsen vid läsning av Parquet-filer. Kolumn namn och data typer läses automatiskt från Parquet-filer.

Exemplet nedan visar de automatiska schema härlednings funktionerna för Parquet-filer. Det returnerar antalet rader i september 2017 utan att ange ett schema.

> [!NOTE]
> Du behöver inte ange kolumner i OpenRowSet WITH-satsen när du läser Parquet-filer. I så fall använder Query-tjänsten SQL på begäran metadata i Parquet-filen och bind kolumner efter namn.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Fråga partitionerade data

Den data mängd som anges i det här exemplet är uppdelad (partitionerad) i separata undermappar. Du kan fokusera på specifika partitioner med funktionen filsökväg. I det här exemplet visas pris belopp per år, månad och payment_type under de första tre månaderna av 2017.

> [!NOTE]
> SQL-frågan på begäran är kompatibel med Hive/Hadoop-partitionerings schema.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Typ mappning

Parquet-filer innehåller typ beskrivningar för varje kolumn. I följande tabell beskrivs hur Parquet-typer mappas till inbyggda SQL-typer.

| Typ av Parquet | Parquet logiska typ (anteckning) | SQL-datatyp |
| --- | --- | --- |
| BOOLESKT | | bit |
| BINÄR/BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLYTA | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(utf8-sortering) |
| BINARY |NOLLÄNGD |varchar \*(utf8-sortering) |
| BINARY |RÄKNING|varchar \*(utf8-sortering) |
| BINARY |UUID |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar (max) \*(utf8-sortering) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVALL |varchar (max), serialiserad till standardiserat format |
| INT32 |INT (8, sant) |smallint |
| INT32 |INT (16, sant) |smallint |
| INT32 |INT (32, sant) |int |
| INT32 |INT (8, falskt) |tinyint |
| INT32 |INT (16, falskt) |int |
| INT32 |INT (32, falskt) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TID (MILLIS)|time |
| INT64 |INT (64, sant) |bigint |
| INT64 |INT (64, falskt) |decimal (20, 0) |
| INT64 |DECIMAL |decimal |
| INT64 |TID (MICROS/NANO) |time |
|INT64 |TIDSSTÄMPEL (MILL/MICROS/NANOS) |datetime2 |
|[Komplex typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar (max), serialiserad till JSON |
|[Komplex typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAPPA|varchar (max), serialiserad till JSON |

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig att [fråga Parquet-kapslade typer](query-parquet-nested-types.md).
