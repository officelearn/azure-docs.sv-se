---
title: Fråga efter data i Azure Data Lake med Azure Datautforskaren
description: Lär dig hur du frågar efter data i Azure Data Lake att använda Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: b0056df16dccaf1dc7e94aad1a2c6c262ffd89ee
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383364"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Fråga efter data i Azure Data Lake med Azure Datautforskaren (förhands granskning)

Azure Data Lake Storage är en mycket skalbar och kostnads effektiv data Lake-lösning för stor data analys. Här kombineras kraften hos ett högpresterande filsystem med storskalighet och ekonomi som hjälper dig att korta insiktstiden. Med Data Lake Storage Gen2 utökas kapaciteten i Azure Blob Storage och optimeras för analysarbetsbelastningar.
 
Azure Datautforskaren integreras med Azure Blob Storage och Azure Data Lake Storage Gen2, vilket ger snabb, cachelagrad och indexerad åtkomst till data i sjön. Du kan analysera och fråga efter data i sjön utan föregående inhämtning i Azure Datautforskaren. Du kan också fråga över inmatade och inmatade interna sjö data samtidigt.  

> [!TIP]
> Bästa prestanda för frågor kräver data inmatning i Azure Datautforskaren. Möjligheten att fråga data i Azure Data Lake Storage Gen2 utan föregående inmatning bör endast användas för historiska data eller data som sällan efter frågas.
 
## <a name="optimize-query-performance-in-the-lake"></a>Optimera frågornas prestanda i sjö 

* Partitionera data för bättre prestanda och optimerad fråge tid.
* Komprimera data för bättre prestanda (gzip för bästa komprimering, lz4 för bästa prestanda).
* Använd Azure Blob Storage eller Azure Data Lake Storage Gen2 med samma region som ditt Azure Datautforskaren-kluster. 

## <a name="create-an-external-table"></a>Skapa en extern tabell

 > [!NOTE]
 > Lagrings konton som stöds för närvarande är Azure Blob Storage eller Azure Data Lake Storage Gen2. Data format som stöds för närvarande är JSON, CSV, TSV och txt.

1. `.create external table` Använd kommandot för att skapa en extern tabell i Azure datautforskaren. Ytterligare kommandon för externa tabeller som `.show`, `.drop`och `.alter` är dokumenterade i [kommandon för externa tabeller](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * Ökad prestanda förväntas med mer detaljerad partitionering. Till exempel kommer frågor över externa tabeller med dagliga partitioner att ha bättre prestanda än de frågor som har månatliga partitionerade tabeller.
    > * När du definierar en extern tabell med partitioner förväntas lagrings strukturen vara identisk.
Om tabellen till exempel är definierad med en DateTime-partition i formatet ÅÅÅÅ/MM/DD (standard), ska sökvägen till URI- *container1 vara/åååå/mm/dd/all_exported_blobs*. 
    > * Om den externa tabellen är partitionerad av en datetime-kolumn, ska du alltid inkludera ett tids filter för ett stängt intervall i frågan (till exempel frågan – `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` bör utföra bättre än det här (öppna intervallet) en- `ArchivedProducts | where Timestamp > ago(1h)` ). 

1. Den externa tabellen visas i den vänstra rutan i webb gränssnittet

    ![extern tabell i webb gränssnittet](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Skapa en extern tabell med JSON-format

Du kan skapa en extern tabell med JSON-format. Mer information finns i [externa tabell kommandon](/azure/kusto/management/externaltables)

1. Använd kommandot för att skapa en tabell med namnet *ExternalTableJson:* `.create external table`

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. JSON-formatet kräver ett andra steg i skapandet av mappning till kolumner som visas nedan. I följande fråga skapar du en angiven JSON-mappning med namnet *mappingName*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Behörigheter för externa tabeller
 
* Databas användaren kan skapa en extern tabell. Tabellens skapare blir automatiskt tabell administratör.
* Klustret, databasen eller tabell administratören kan redigera en befintlig tabell.
* En databas användare eller läsare kan fråga en extern tabell.
 
## <a name="query-an-external-table"></a>Fråga en extern tabell
 
Om du vill fråga en extern tabell använder `external_table()` du funktionen och anger tabell namnet som funktions argument. Resten av frågan är standard språk för Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> IntelliSense stöds inte för närvarande på externa tabell frågor.

### <a name="query-an-external-table-with-json-format"></a>Fråga en extern tabell med JSON-format

Om du vill fråga en extern tabell med JSON-format `external_table()` använder du funktionen och anger både tabell namn och mappnings namn som funktions argument. I frågan nedan, om *mappingName* inte har angetts, används en mappning som du skapade tidigare.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Fråga externa och inmatade data tillsammans

Du kan fråga både externa tabeller och inmatade data tabeller i samma fråga. Du [`join`](/azure/kusto/query/joinoperator) [eller`union`](/azure/kusto/query/unionoperator) den externa tabellen med ytterligare data från Azure datautforskaren, SQL-servrar eller andra källor. Använd en [`let( ) statement`](/azure/kusto/query/letstatement) för att tilldela ett kort namn till en extern tabell referens.

I exemplet nedan är *produkter* en inmatad data tabell och *ArchivedProducts* är en extern tabell som innehåller data i Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Fråga *TaxiRides* extern tabell i hjälp klustret

Exempel data uppsättningen *TaxiRides* innehåller New York City-data från [NYC taxi-och limousine-kommission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Skapa extern tabell *TaxiRides* 

> [!NOTE]
> I det här avsnittet beskrivs frågan som används för att skapa den externa *TaxiRides* -tabellen i *Hjälp* klustret. Eftersom den här tabellen redan har skapats kan du hoppa över det här avsnittet och köra [fråga *TaxiRides* externa tabell data](#query-taxirides-external-table-data). 

1. Följande fråga användes för att skapa den externa tabellen *TaxiRides* i hjälp klustret. 

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
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. Den resulterande tabellen skapades i *Hjälp* klustret:

    ![TaxiRides extern tabell](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Fråga *TaxiRides* externa tabell data 

Logga in för [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) att fråga den externa *TaxiRides* -tabellen. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Fråga *TaxiRides* extern tabell utan partitionering

[Kör den här frågan](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) på den externa tabellens *TaxiRides* om du vill visa en del av varje veckodag i hela data uppsättningen. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Den här frågan visar veckans vanligaste dag. Eftersom data inte har partitionerats kan det ta lång tid för den här frågan att returnera resultat (upp till flera minuter).

![rendera en icke-partitionerad fråga](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Fråga TaxiRides extern tabell med partitionering 

[Kör den här frågan](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) på den externa tabellen *TaxiRides* som visar taxi hytt typer (gult eller grönt) som används i januari 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Den här frågan använder partitionering, vilket optimerar frågans tid och prestanda. Frågefilter i en partitionerad kolumn (pickup_datetime) och returnerar resultat på några sekunder.

![Rendera partitionerad fråga](media/data-lake-query-data/taxirides-with-partition.png)
  
Du kan skriva ytterligare frågor som ska köras på den externa tabellen *TaxiRides* och läsa mer om data. 

## <a name="next-steps"></a>Nästa steg

Fråga dina data i Azure Data Lake med Azure Datautforskaren. Lär dig att [skriva frågor](write-queries.md) och härleda ytterligare insikter från dina data.
