---
title: Fråga mappar och flera CSV-filer med SQL på begäran (förhandsgranskning)
description: SQL on-demand (preview) stöder läsning av flera filer/mappar med hjälp av jokertecken, som liknar de jokertecken som används i Windows OS.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457373"
---
# <a name="query-folders-and-multiple-csv-files"></a>Fråga mappar och flera CSV-filer  

I den här artikeln får du lära dig hur du skriver en fråga med SQL on-demand (preview) i Azure Synapse Analytics.

SQL on-demand stöder läsning av flera filer/mappar med jokertecken, som liknar de jokertecken som används i Windows OS. Det finns dock större flexibilitet eftersom flera jokertecken är tillåtna.

## <a name="prerequisites"></a>Krav

Innan du läser resten av den här artikeln, se till att granska de artiklar som anges nedan:

- [Första gången setup](query-data-storage.md#first-time-setup)
- [Krav](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Läsa flera filer i mappen

Du ska använda mappen *csv/taxi* för att följa exempelfrågorna. Den innehåller NYC Taxi - Yellow Taxi Trip Records data från juli 2016 till juni 2018.

Filer i *csv /taxi* är uppkallade efter år och månad:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Varje fil har följande struktur:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Läsa alla filer i mappen
    
Exemplet nedan läser alla NYC Yellow Taxi datafiler från *csv / taxi* mappen och returnerar det totala antalet passagerare och rider per år. Den visar också användning av aggregerade funktioner.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som nås med den enda OPENROWSET måste ha samma struktur (dvs. antal kolumner och deras datatyper).

### <a name="read-subset-of-files-in-folder"></a>Läsa delmängd av filer i mappen

Exemplet nedan läser 2017 NYC Yellow Taxi datafiler från *csv / taxi-mappen* med hjälp av ett jokertecken och returnerar det totala biljettpriset per betalningstyp.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Alla filer som nås med den enda OPENROWSET måste ha samma struktur (dvs. antal kolumner och deras datatyper).

## <a name="read-folders"></a>Läsa mappar

Sökvägen som du anger till OPENROWSET kan också vara en sökväg till en mapp. Följande avsnitt innehåller dessa frågetyper.

### <a name="read-all-files-from-specific-folder"></a>Läsa alla filer från en viss mapp

Du kan läsa alla filer i en mapp med hjälp av jokertecken på filnivå som visas i [Läs alla filer i mappen](#read-all-files-in-folder). Men det finns ett sätt att fråga en mapp och använda alla filer i den mappen.

Om sökvägen i OPENROWSET pekar på en mapp används alla filer i den mappen som en källa för frågan. Följande fråga kommer att läsa alla filer i *csv / taxi* mappen.

> [!NOTE]
> Observera förekomsten av / i slutet av sökvägen i frågan nedan. Det betecknar en mapp. Om / utelämnas, kommer frågan att rikta en fil som heter *taxi* istället.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som nås med den enda OPENROWSET måste ha samma struktur (dvs. antal kolumner och deras datatyper).

### <a name="read-all-files-from-multiple-folders"></a>Läsa alla filer från flera mappar

Det är möjligt att läsa filer från flera mappar med hjälp av ett jokertecken. Följande fråga kommer att läsa alla filer från alla mappar som finns i *csv-mappen* som har namn som börjar med *t* och slutar med *i*.

> [!NOTE]
> Observera förekomsten av / i slutet av sökvägen i frågan nedan. Det betecknar en mapp. Om / utelämnas, kommer frågan målfiler som heter *t&ast;i* istället.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som nås med den enda OPENROWSET måste ha samma struktur (dvs. antal kolumner och deras datatyper).

Eftersom du bara har en mapp som matchar villkoren är frågeresultatet detsamma som [Läs alla filer i mappen](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Flera jokertecken

Du kan använda flera jokertecken på olika sökvägsnivåer. Du kan till exempel berika föregående fråga för att läsa filer med 2017-data, från alla mappar som namn börjar med *t* och slutar med *i*.

> [!NOTE]
> Observera förekomsten av / i slutet av sökvägen i frågan nedan. Det betecknar en mapp. Om / utelämnas, kommer frågan målfiler som heter *t&ast;i* istället.
> Det finns en maximal gräns på 10 jokertecken per fråga.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som nås med den enda OPENROWSET måste ha samma struktur (dvs. antal kolumner och deras datatyper).

Eftersom du bara har en mapp som matchar villkoren är frågeresultatet detsamma som [Läsundergrupp av filer i mappen](#read-subset-of-files-in-folder) och Läs alla filer från den specifika [mappen](#read-all-files-from-specific-folder). Mer komplexa scenarier för jokerteckenanvändning beskrivs i [Frågeparkettfiler](query-parquet-files.md).

## <a name="next-steps"></a>Nästa steg

Mer information finns i artikeln i [Frågespecifika filer.](query-specific-files.md)
