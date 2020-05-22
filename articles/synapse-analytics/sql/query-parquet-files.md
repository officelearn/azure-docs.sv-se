---
title: Fråga Parquet-filer med SQL på begäran (för hands version)
description: I den här artikeln får du lära dig hur du frågar Parquet-filer med SQL på begäran (för hands version).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e9731b869b20c7d8cfc3b1e234711c818a2b7422
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744242"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Fråga Parquet-filer med SQL on-demand (för hands version) i Azure Synapse Analytics

I den här artikeln får du lära dig hur du skriver en fråga med SQL på begäran (för hands version) som kommer att läsa Parquet-filer.

## <a name="prerequisites"></a>Krav

Ditt första steg är att **skapa en databas** med en data källa som refererar till [NYC gul taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -lagrings konto. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används i de här exemplen.

## <a name="dataset"></a>Datauppsättning

[NYC gul taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -datauppsättning används i det här exemplet. Du kan fråga Parquet-filer på samma sätt som du [läser CSV-filer](query-parquet-files.md). Den enda skillnaden är att `FILEFORMAT` parametern ska vara inställd på `PARQUET` . Exemplen i den här artikeln visar information om hur du läser Parquet-filer.

## <a name="query-set-of-parquet-files"></a>Fråga uppsättning Parquet-filer

Du kan bara ange intressanta kolumner när du frågar Parquet-filer.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatisk schema härledning

Du behöver inte använda OpenRowSet WITH-satsen vid läsning av Parquet-filer. Kolumn namn och data typer läses automatiskt från Parquet-filer.

Exemplet nedan visar de automatiska schema härlednings funktionerna för Parquet-filer. Det returnerar antalet rader i september 2017 utan att ange ett schema.

> [!NOTE]
> Du behöver inte ange kolumner i OpenRowSet WITH-satsen när du läser Parquet-filer. I så fall använder Query-tjänsten SQL på begäran metadata i Parquet-filen och bind kolumner efter namn.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Fråga partitionerade data

Den data mängd som anges i det här exemplet är uppdelad (partitionerad) i separata undermappar. Du kan fokusera på specifika partitioner med funktionen filsökväg. I det här exemplet visas pris belopp per år, månad och payment_type under de första tre månaderna av 2017.

> [!NOTE]
> SQL-frågan på begäran är kompatibel med Hive/Hadoop-partitionerings schema.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
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
| BINARY |UTF8 |varchar \* (utf8-sortering) |
| BINARY |NOLLÄNGD |varchar \* (utf8-sortering) |
| BINARY |RÄKNING|varchar \* (utf8-sortering) |
| BINARY |UUID |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar (max) \* (utf8-sortering) |
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
