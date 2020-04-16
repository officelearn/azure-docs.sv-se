---
title: Fråga parquet-filer med SQL på begäran (förhandsgranskning)
description: I den här artikeln får du lära dig att fråga parettfiler med SQL on-demand (preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431701"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Fråga parquet-filer med SQL on-demand (preview) i Azure Synapse Analytics

I den här artikeln får du lära dig att skriva en fråga med SQL on-demand (preview) som kommer att läsa Parquet filer.

## <a name="prerequisites"></a>Krav

Innan du läser resten av den här artikeln, granska följande artiklar:

- [Första gången setup](query-data-storage.md#first-time-setup)
- [Krav](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Datauppsättning

Du kan fråga Parquet filer på samma sätt som du läser CSV filer. Den enda skillnaden är att parametern FILEFORMAT ska ställas in på PARKETT. Exempel i denna artikel visar detaljerna i att läsa Parquet filer.

> [!NOTE]
> Du behöver inte ange kolumner i OPENROWSET WITH-satsen när du läser parkettfiler. SQL on-demand använder metadata i parquetfilen och binder kolumner efter namn.

Du ska använda mappen *parkett / taxi* för exempelfrågor. Den innehåller NYC Taxi - Yellow Taxi Trip Records data från juli 2016. juni 2018.

Data partitioneras efter år och månad och mappstrukturen är följande:

- år=2016
  - månad=6
  - ...
  - månad=12
- år=2017
  - månad=1
  - ...
  - månad=12
- år=2018
  - månad=1
  - ...
  - månad=6

## <a name="query-set-of-parquet-files"></a>Frågeuppsättning med parkettfiler

Du kan bara ange intressekolumner när du frågar parettfiler.

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

## <a name="automatic-schema-inference"></a>Automatisk schemaavferening

Du behöver inte använda OPENROWSET WITH-satsen när du läser Parquet-filer. Kolumnnamn och datatyper läss automatiskt från parettfiler.

Exemplet nedan visar de automatiska schema inferensfunktionerna för parquet-filer. Antalet rader returneras i september 2017 utan att ange ett schema.

> [!NOTE]
> Du behöver inte ange kolumner i OPENROWSET WITH-satsen när du läser Parquet-filer. I så fall använder SQL on-demand Query-tjänsten metadata i parquet-filen och binder kolumner efter namn.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Frågepartitionerade data

Datauppsättningen som anges i det här exemplet delas upp (partitioneras) i separata undermappar. Du kan rikta specifika partitioner med hjälp av filsöken. I det här exemplet visas biljettprisbelopp per år, månad och payment_type för de tre första månaderna 2017.

> [!NOTE]
> SQL on-demand-frågan är kompatibel med Hive/Hadoop-partitioneringsschema.

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

## <a name="type-mapping"></a>Typmappning

Parkettfiler innehåller typbeskrivningar för varje kolumn. I följande tabell beskrivs hur parketttyper mappas till inbyggda SQL-typer.

| Parkett typ | Parkett logisk typ (anteckning) | SQL-datatyp |
| --- | --- | --- |
| Boolean | | bit |
| BINÄR / BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| Flyta | | real |
| INT32 (INT32) | | int |
| INT64 | | bigint |
| INT96 (INT96) | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(UTF8-sortering) |
| BINARY |Sträng |varchar \*(UTF8-sortering) |
| BINARY |Enum|varchar \*(UTF8-sortering) |
| BINARY |Uuid |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(max) \*(UTF8-sortering) |
| BINARY |BSON (på) |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |Intervall |varchar(max), serialiserat till standardiserat format |
| INT32 (INT32) |INT(8, sant) |smallint |
| INT32 (INT32) |INT(16, sant) |smallint |
| INT32 (INT32) |INT(32, sant) |int |
| INT32 (INT32) |INT(8, falskt) |tinyint |
| INT32 (INT32) |INT(16, falskt) |int |
| INT32 (INT32) |INT(32, falskt) |bigint |
| INT32 (INT32) |DATE |date |
| INT32 (INT32) |DECIMAL |decimal |
| INT32 (INT32) |TID (MILLIS)|time |
| INT64 |INT(64, sant) |bigint |
| INT64 |INT(64, falskt) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TID (MIKROS / NANOS) |time |
|INT64 |TIDSSTÄMPEL (MILLIS / MICROS / NANOS) |datetime2 |
|[Komplex typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Lista |varchar(max), serialiserat till JSON |
|[Komplex typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Karta|varchar(max), serialiserat till JSON |

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel för att lära dig hur [du frågar parett kapslade typer](query-parquet-nested-types.md).
