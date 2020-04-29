---
title: Använda fil-metadata i frågor
description: Funktionen OpenRowSet innehåller fil-och Sök vägs information om alla filer som används i frågan för att filtrera eller analysera data baserat på fil namn och/eller mappsökväg.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431558"
---
# <a name="using-file-metadata-in-queries"></a>Använda fil-metadata i frågor

SQL-tjänsten på begäran kan adressera flera filer och mappar enligt beskrivningen i artikeln [frågenoder och flera filer](query-folders-multiple-csv-files.md) . I den här artikeln får du lära dig hur du använder metadatainformation om fil-och mappnamn i frågorna.

Ibland kan du behöva veta vilken fil eller mapp källa som motsvarar en speciell rad i resultat uppsättningen.

Du kan använda funktionen `filepath` och `filename` för att returnera fil namn och/eller sökvägen i resultat uppsättningen. Eller så kan du använda dem för att filtrera data baserat på fil namnet och/eller mappsökvägen. Dessa funktioner beskrivs i funktionen syntax i avsnittet [filename](develop-storage-files-overview.md#filename-function) och funktionen fil [Sök väg](develop-storage-files-overview.md#filepath-function). Nedan hittar du korta beskrivningar utmed exempel.

## <a name="prerequisites"></a>Krav

Läs igenom följande krav innan du läser resten av den här artikeln:

- [Installation vid första tiden](query-data-storage.md#first-time-setup)
- [Förutsättningar](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>Sökväg

Den här funktionen returnerar fil namnet som raden kommer från.

Följande exempel läser de NYC gula taxi-datafilerna för de senaste tre månaderna av 2017 och returnerar antalet åsidosättningar per fil. Delen OpenRowSet i frågan anger vilka filer som ska läsas.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

I följande exempel visas hur *fil namn ()* kan användas i WHERE-satsen för att filtrera filerna som ska läsas. Den har åtkomst till hela mappen i OpenRowSet-delen av frågan och filtrerar filer i WHERE-satsen.

Resultatet är detsamma som i föregående exempel.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

Funktionen filsökväg returnerar en fullständig eller partiell sökväg:

- När den anropas utan en parameter, returnerar den den fullständiga fil Sök vägen som raden kommer från.
- När den anropas med en parameter, returneras en del av sökvägen som matchar jokertecknet för den position som anges i parametern. Parameter värde 1 skulle till exempel returnera en del av sökvägen som matchar det första jokertecknet.

Följande exempel läser NYC Yellow taxi-datafiler under de tre senaste månaderna av 2017. Det returnerar antalet åsidosättningar per fil Sök väg. Delen OpenRowSet i frågan anger vilka filer som ska läsas.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

I följande exempel visas hur *sökvägen ()* kan användas i WHERE-satsen för att filtrera filerna som ska läsas.

Du kan använda jokertecken i delen OpenRowSet i frågan och filtrera filerna i WHERE-satsen. Resultatet är detsamma som i föregående exempel.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Nästa steg

I nästa artikel får du lära dig hur du [frågar Parquet-filer](query-parquet-files.md).
