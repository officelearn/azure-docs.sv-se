---
title: Fråga efter data i Azure Data Lake med Azure Data Explorer
description: Lär dig mer om att fråga efter data i Azure Data Lake med Datautforskaren i Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453182"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Fråga efter data i Azure Data Lake med Azure Data Explorer (förhandsversion)

Azure Data Lake Storage är en mycket pålitlig och kostnadseffektiv data lake-lösning för analys av stordata. Här kombineras kraften hos ett högpresterande filsystem med storskalighet och ekonomi som hjälper dig att korta insiktstiden. Med Data Lake Storage Gen2 utökas kapaciteten i Azure Blob Storage och optimeras för analysarbetsbelastningar.
 
Azure Data Explorer kan integreras med Azure Blob Storage och Azure Data Lake Storage Gen2, vilket ger snabb, cachelagras och indexeras åtkomst till data i sjön. Du kan analysera och fråga efter data i sjön utan tidigare samlats in till Datautforskaren i Azure. Du kan också fråga i infogade och uningested interna lake samtidigt.  

> [!TIP]
> Bästa frågeprestanda kräver datapåfyllning till Datautforskaren i Azure. Möjlighet att fråga efter data i Azure Data Lake Storage Gen2 utan tidigare inmatning bör endast användas för historiska data eller data som används sällan.
 
## <a name="optimize-query-performance-in-the-lake"></a>Optimera prestanda för frågor i sjön 

* Partitionera data för bättre prestanda och optimerad fråga körs.
* Komprimera data för bättre prestanda (gzip för bästa komprimerings, lz4 för bästa prestanda).
* Använda Azure Blob Storage eller Azure Data Lake Storage Gen2 med samma region som din Azure Data Explorer-kluster. 

## <a name="create-an-external-table"></a>Skapa en extern tabell

1. Använd den `.create external table` kommando för att skapa en extern tabell i Datautforskaren i Azure. Ytterligare extern tabell kommandon som `.show`, `.drop`, och `.alter` finns dokumenterade i [extern tabell kommandon](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    Den här frågan skapar dagliga *container1/yyyy/MM/dd/all_exported_blobs.csv*. Bättre prestanda förväntas med mer detaljerade partitionering. Till exempel har frågor via externa tabeller med dagliga partitioner, till exempel de som anges ovan bättre prestanda än dessa frågor med månatliga partitionerade tabeller.

    > [!NOTE]
    > Konton som för närvarande stöds är Azure Blob Storage eller Azure Data Lake Storage Gen2. Dataformat som stöds för närvarande är csv, TVs och txt.

1. Den externa tabellen visas i den vänstra rutan i Webbgränssnittet

    ![extern tabell i webbgränssnittet](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>Extern tabellbehörigheter
 
* Databasanvändare kan skapa en extern tabell. Den tabell som har skaparen blir automatiskt tabell-administratör.
* Klustret, databasen eller tabellen administratör kan redigera en befintlig tabell.
* En databasanvändare eller läsare kan fråga en extern tabell.
 
## <a name="query-an-external-table"></a>Fråga en extern tabell
 
Om du vill fråga en extern tabell, använder de `external_table()` fungerar och ger tabellnamn som funktionsargument. Resten av frågan är standardspråket för Kusto-fråga.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> IntelliSense stöds inte för närvarande på extern tabell frågor.

## <a name="query-external-and-ingested-data-together"></a>Skicka frågor till externa och insamlade data tillsammans

Du kan fråga både externa tabeller och inmatade tabeller inom samma fråga. Du [ `join` ](/azure/kusto/query/joinoperator) eller [ `union` ](/azure/kusto/query/unionoperator) den externa tabellen med ytterligare data från Azure Data Explorer, SQL-servrar eller andra källor. Använd en [ `let( ) statement` ](/azure/kusto/query/letstatement) tilldela en snabbformat till en extern tabell som referens.

I exemplet nedan, *produkter* är en datatabell med insamlade och *ArchivedProducts* är en extern tabell som innehåller data i Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Fråga *TaxiRides* extern tabell i hjälpklustret

Den *TaxiRides* exempeldatauppsättning innehåller New York City taxi-data från [NYC Taxi och Limousine kommissionen](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Skapa extern tabell *TaxiRides* 

> [!NOTE]
> Det här avsnittet visar frågan som används för att skapa den *TaxiRides* extern tabell i den *hjälpa* kluster. Eftersom den här tabellen har redan skapats kan du hoppa över det här avsnittet och utföra [fråga *TaxiRides* externa tabelldata](#query-taxirides-external-table-data). 

1. Följande fråga har använts för att skapa den externa tabellen *TaxiRides* i hjälpklustret. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. Den resulterande tabellen har skapats i den *hjälpa* klustret:

    ![TaxiRides extern tabell](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Fråga *TaxiRides* data från externa tabeller 

Logga in på [ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples) att fråga den *TaxiRides* extern tabell. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Fråga *TaxiRides* extern tabell utan partitionering

[Köra den här frågan](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) på den externa tabellen *TaxiRides* till att avbilda cykel för varje dag i veckan, över hela datauppsättningen. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Den här frågan visar de mest använda dag i veckan. Eftersom data inte är partitionerad, kan den här frågan ta lång tid att returnera resultat (upp till flera minuter).

![Rendera icke-partitionerad fråga](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Fråga TaxiRides extern tabell med partitionering 

[Köra den här frågan](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) på den externa tabellen *TaxiRides* som visar taxi cab typer (gult eller grönt) används i januari 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Den här frågan använder partitionering, vilket optimerar frågetiden och prestanda. Frågan filtrerar på en partitionerad kolumn (pickup_datetime) och returnerar resultat på några sekunder.

![Rendera partitionerade fråga](media/data-lake-query-data/taxirides-with-partition.png)
  
Du kan skriva ytterligare frågor som ska köras på den externa tabellen *TaxiRides* och lär dig mer om data. 

## <a name="next-steps"></a>Nästa steg

Fråga dina data i Azure Data Lake med Datautforskaren i Azure. Lär dig hur du [skriva frågor](write-queries.md) och få ytterligare analyser från dina data.