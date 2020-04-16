---
title: Använda filmetadata i frågor
description: Openrowset-funktionen innehåller fil- och sökvägsinformation om varje fil som används i frågan för att filtrera eller analysera data baserat på filnamn och/eller mappsökväg.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431558"
---
# <a name="using-file-metadata-in-queries"></a>Använda filmetadata i frågor

Sql on-demand Query-tjänsten kan adressera flera filer och mappar enligt beskrivningen i artikeln [Frågemappar och flera filer.](query-folders-multiple-csv-files.md) I den här artikeln får du lära dig hur du använder metadatainformation om fil- och mappnamn i frågorna.

Ibland kan du behöva veta vilken fil eller mappkälla som korrelerar till en viss rad i resultatuppsättningen.

Du kan `filepath` använda `filename` funktionen och returnera filnamn och/eller sökvägen i resultatuppsättningen. Du kan också använda dem för att filtrera data baserat på filnamnet och/eller mappsökvägen. Dessa funktioner beskrivs i funktionen [filnamn](develop-storage-files-overview.md#filename-function) i syntaxavsnittet och [filsökfunktionen](develop-storage-files-overview.md#filepath-function). Nedan hittar du korta beskrivningar längs prover.

## <a name="prerequisites"></a>Krav

Innan du läser resten av den här artikeln bör du läsa följande förutsättningar:

- [Första gången setup](query-data-storage.md#first-time-setup)
- [Krav](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>Filnamn

Den här funktionen returnerar filnamnet som raden kommer från.

Följande exempel läser NYC Yellow Taxi-datafilerna under de sista tre månaderna av 2017 och returnerar antalet turer per fil. Openrowset-delen av frågan anger vilka filer som ska läsas.

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

I följande exempel visas hur *filnamn()* kan användas i WHERE-satsen för att filtrera de filer som ska läsas. Den kommer åt hela mappen i OPENROWSET-delen av frågan och filtrerar filer i WHERE-satsen.

Dina resultat kommer att vara desamma som föregående exempel.

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

Filsökfunktionen returnerar en fullständig eller partiell sökväg:

- När den anropas utan parameter returneras den fullständiga filsökvägen som raden kommer från.
- När den anropas med en parameter returneras en del av sökvägen som matchar jokertecknet på den position som anges i parametern. Parametervärde 1 returnerar till exempel en del av sökvägen som matchar det första jokertecknet.

Följande exempel läser NYC Yellow Taxi datafiler för de sista tre månaderna av 2017. Antalet turer per filsökväg returneras. Openrowset-delen av frågan anger vilka filer som ska läsas.

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

I följande exempel visas hur *filepath()* kan användas i WHERE-satsen för att filtrera de filer som ska läsas.

Du kan använda jokertecken i OPENROWSET-delen av frågan och filtrera filerna i WHERE-satsen. Dina resultat kommer att vara desamma som föregående exempel.

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

I nästa artikel får du lära dig att [fråga Parquet filer](query-parquet-files.md).
