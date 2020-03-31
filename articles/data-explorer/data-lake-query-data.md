---
title: Fråga data i Azure Data Lake med Azure Data Explorer
description: Lär dig hur du frågar data i Azure Data Lake med Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161757"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Fråga data i Azure Data Lake med Azure Data Explorer

Azure Data Lake Storage är en mycket skalbar och kostnadseffektiv datasjölösning för stordataanalys. Den kombinerar kraften i ett högpresterande filsystem med massiv skala och ekonomi för att hjälpa dig att snabba din tid till insikt. Data Lake Storage Gen2 utökar Azure Blob Storage-funktioner och är optimerad för analysarbetsbelastningar.
 
Azure Data Explorer integreras med Azure Blob Storage och Azure Data Lake Storage (Gen1 och Gen2), vilket ger snabb, cachelagd och indexerad åtkomst till data i sjön. Du kan analysera och fråga data i sjön utan föregående inmatning i Azure Data Explorer. Du kan också fråga över intas och unintased native lake data samtidigt.  

> [!TIP]
> Den bästa frågeprestanda kräver datainmatning i Azure Data Explorer. Möjligheten att fråga externa data utan tidigare inmatning bör endast användas för historiska data eller data som sällan efterfrågas. [Optimera frågeprestanda i sjön](#optimize-your-query-performance) för bästa resultat.
 

## <a name="create-an-external-table"></a>Skapa en extern tabell

 > [!NOTE]
 > Lagringskonton som stöds är Azure Blob Storage eller Azure Data Lake Storage (Gen1 och Gen2).

1. Använd `.create external table` kommandot för att skapa en extern tabell i Azure Data Explorer. Ytterligare externa tabellkommandon, `.drop`till `.alter` exempel `.show`, och dokumenteras i [externa tabellkommandon](/azure/kusto/management/externaltables).

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
    > * Ökad prestanda förväntas med mer detaljerad partitionering. Frågor över externa tabeller med dagliga partitioner har till exempel bättre prestanda än de frågor med månatliga partitionerade tabeller.
    > * När du definierar en extern tabell med partitioner förväntas lagringsstrukturen vara identisk.
Om tabellen till exempel definieras med en DateTime-partition i yyyy/MM/dd-format (standard), ska sökvägen till URI-lagringsfilen vara *container1/yyyy/MM/dd/all_exported_blobs*. 
    > * Om den externa tabellen partitioneras efter en datetimekolumn ska du alltid inkludera ett tidsfilter för ett stängt område i frågan (till exempel bör frågan - `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` - prestera bättre än den här (öppnat intervall) en - `ArchivedProducts | where Timestamp > ago(1h)` ). 
    > * Alla [inmatningsformat som stöds](ingest-data-overview.md#supported-data-formats) kan efterfrågas med hjälp av externa tabeller.

1. Den externa tabellen visas i den vänstra rutan i webbgränssnittet

    ![extern tabell i webbgränssnittet](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Skapa en extern tabell med json-format

Du kan skapa en extern tabell med json-format. Mer information finns i [Externa tabellkommandon](/azure/kusto/management/externaltables)

1. Använd `.create external table` kommandot för att skapa en tabell med namnet *ExternalTableJson:*

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
 
1. Json-formatet kräver ett andra steg för att skapa mappning till kolumner som visas nedan. Skapa en specifik json-mappning med namnet *mappingName*i följande fråga:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Behörigheter för externa tabeller
 
* Databasanvändaren kan skapa en extern tabell. Tabellskaparen blir automatiskt tabelladministratör.
* Kluster-, databas- eller tabelladministratören kan redigera en befintlig tabell.
* Alla databasanvändare eller läsare kan fråga en extern tabell.
 
## <a name="query-an-external-table"></a>Fråga en extern tabell
 
Om du vill fråga `external_table()` efter en extern tabell använder du funktionen och anger tabellnamnet som funktionsargument. Resten av frågan är standard Kusto frågespråk.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense stöds för närvarande inte i externa tabellfrågor.

### <a name="query-an-external-table-with-json-format"></a>Fråga en extern tabell med json-format

Om du vill fråga en extern `external_table()` tabell med json-format använder du funktionen och anger både tabellnamn och mappningsnamn som funktionsargument. Om *mappnamn* inte anges används en mappning som du tidigare har skapat om du inte har angett en mappning som du tidigare har skapat.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Fråga externa och intas data tillsammans

Du kan fråga både externa tabeller och intjesterade datatabeller i samma fråga. Du [`join`](/azure/kusto/query/joinoperator) [`union`](/azure/kusto/query/unionoperator) eller den externa tabellen med ytterligare data från Azure Data Explorer, SQL-servrar eller andra källor. Använd [`let( ) statement`](/azure/kusto/query/letstatement) a för att tilldela ett korttidsnamn till en extern tabellreferens.

I exemplet nedan är *produkter* en inmatad datatabell och *ArchivedProducts* är en extern tabell som innehåller data i Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Fråga *TaxiRides* externa tabell i hjälpklustret

*Den TaxiRides* prov datauppsättning innehåller New York City taxi data från [NYC Taxi och Limousine kommissionen](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Skapa externa tabell *TaxiRides* 

> [!NOTE]
> I det här avsnittet visas frågan som används för att skapa den externa *tabellen TaxiRides* i hjälpklustret. *help* Eftersom den här tabellen redan har skapats kan du hoppa över det här avsnittet och utföra [fråga *TaxiRides* externa tabelldata](#query-taxirides-external-table-data). 

1. Följande fråga användes för att skapa den externa tabellen *TaxiRides* i hjälpklustret. 

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
1. Den resulterande tabellen *help* skapades i hjälpklustret:

    ![TaxiRides externt bord](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Fråga *TaxiRa* externa tabelldata 

Logga in [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) för att fråga den externa *tabellen TaxiRides.* 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Fråga *TaxiRides* extern tabell utan partitionering

[Kör den här frågan](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) på den externa tabellen *TaxiRides* för att visa turer för varje dag i veckan, över hela datauppsättningen. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Den här frågan visar den mest trafikerade dagen i veckan. Eftersom data inte partitioneras kan den här frågan ta lång tid att returnera resultat (upp till flera minuter).

![göra icke-partitionerad fråga](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Fråga TaxiRides extern tabell med partitionering 

[Kör den här frågan](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) på den externa tabellen *TaxiRides* som visar taxihytttyper (gul eller grön) som användes i januari 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Den här frågan använder partitionering, vilket optimerar frågetid och prestanda. Frågefiltren i en partitionerad kolumn (pickup_datetime) och returnerar resultat på några sekunder.

![återge partitionerad fråga](media/data-lake-query-data/taxirides-with-partition.png)
  
Du kan skriva ytterligare frågor att köra på den externa tabellen *TaxiRides* och lära dig mer om data. 

## <a name="optimize-your-query-performance"></a>Optimera frågeprestanda

Optimera frågeprestanda i sjön med hjälp av följande metodtips för att fråga externa data. 
 
### <a name="data-format"></a>Dataformat
 
Använd ett kolumnformat för analytiska frågor sedan:
* Endast de kolumner som är relevanta för en fråga kan läsas. 
* Kolumnkodningstekniker kan minska datastorleken avsevärt.  
Azure Data Explorer stöder parett- och ORC-columnarformat. Parkettformat föreslås på grund av optimerad implementering. 
 
### <a name="azure-region"></a>Azure-region 
 
Kontrollera att externa data finns i samma Azure-region som azure data explorer-klustret. Detta minskar kostnaderna och datahämtningstiden.
 
### <a name="file-size"></a>Filstorlek
 
Optimal filstorlek är hundratals Mb (upp till 1 Gb) per fil. Undvik många små filer som kräver onödiga omkostnader, till exempel långsammare filuppräkningsprocess och begränsad användning av columnar-format. Observera att antalet filer bör vara större än antalet CPU-kärnor i Azure Data Explorer-klustret. 
 
### <a name="compression"></a>Komprimering
 
Använd komprimering för att minska mängden data som hämtas från fjärrlagringen. För Parkettformat, använd den interna parkettkompressionsmekanismen som komprimerar kolumngrupper separat, vilket gör att du kan läsa dem separat. För att validera användningen av komprimeringsmekanism, kontrollera<filename>att filerna heter på<filename>följande sätt: " .gz.parkquet" eller " .snappy.parkquet" i motsats till "<filename>.parkquet.gz"). 
 
### <a name="partitioning"></a>Partitionering
 
Ordna dina data med hjälp av "mapp"-partitioner som gör att frågan kan hoppa över irrelevanta sökvägar. När du planerar partitionering med tanke på filstorlek och vanliga filter i dina frågor, till exempel tidsstämpel eller klient-ID.
 
### <a name="vm-size"></a>Storlek på virtuell dator
 
Välj VIRTUELLA SKU:er med fler kärnor och högre nätverksdataflöde (minnet är mindre viktigt). Mer information finns i [Välj rätt VM SKU för ditt Azure Data Explorer-kluster](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Nästa steg

Fråga dina data i Azure Data Lake med Azure Data Explorer. Lär dig [att skriva frågor](write-queries.md) och härleda ytterligare insikter från dina data.
