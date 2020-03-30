---
title: Läsa in Contoso-återförsäljardata i ett SQL Analytics-datalager
description: Använd PolyBase- och T-SQL-kommandon för att läsa in två tabeller från Contoso-återförsäljardata i Azure SQL Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 62105b783577d70ae975cf514304d2c564357641
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351472"
---
# <a name="load-contoso-retail-data-to-a-sql-analytics-data-warehouse"></a>Läsa in Contoso-återförsäljardata i ett SQL Analytics-datalager

I den här självstudien lär du dig att använda PolyBase- och T-SQL-kommandon för att läsa in två tabeller från Contoso-återförsäljardata i ett SQL Analytics-informationslager. 

I den här guiden kommer du att:

1. Konfigurera PolyBase för inläsning från Azure blob storage
2. Läsa in offentliga data i databasen
3. Utför optimeringar när belastningen är klar.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna köra den här självstudien behöver du ett Azure-konto som redan har ett SQL Analytics-datalager. Om du inte har etablerat ett informationslager läser du [Skapa ett informationslager och ange brandväggsregel på servernivå](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>Konfigurera datakällan

PolyBase använder externa T-SQL-objekt för att definiera plats och attribut för externa data. De externa objektdefinitionerna lagras i ditt SQL Analytics-informationslager. Data lagras externt.

## <a name="create-a-credential"></a>Skapa en autentiseringstillstånd

**Hoppa över det här steget** om du läser in Contoso-offentliga data. Du behöver inte säker åtkomst till offentliga data eftersom de redan är tillgängliga för alla.

**Hoppa inte över det här steget** om du använder den här självstudien som en mall för att läsa in dina egna data. Om du vill komma åt data via en autentiseringsuppgift använder du följande skript för att skapa en databasomfattad autentiseringsuppgifter. Använd den sedan när du definierar platsen för datakällan.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

## <a name="create-the-external-data-source"></a>Skapa den externa datakällan

Använd det här kommandot [SKAPA EXTERN DATAKÄLLA](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-ver15) om du vill lagra platsen för data och datatypen. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Om du väljer att göra dina azure blob lagringsbehållare offentliga, kom ihåg att som dataägare kommer du att debiteras för datautgående avgifter när data lämnar datacentret. 
> 

## <a name="configure-the-data-format"></a>Konfigurera dataformatet

Data lagras i textfiler i Azure blob storage och varje fält avgränsas med en avgränsare. I SSMS kör du följande kommandot SKAPA EXTERNT FILFORMAT för att ange formatet på data i textfilerna. Contoso-data är okomprimerade och pipe avgränsas.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="create-the-external-tables"></a>Skapa de externa tabellerna
Nu när du har angett datakällan och filformatet är du redo att skapa de externa tabellerna. 

## <a name="create-a-schema-for-the-data"></a>Skapa ett schema för data
Skapa ett schema om du vill skapa en plats där Contoso-data lagras i databasen.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Skapa de externa tabellerna

Kör följande skript för att skapa externa dimproduct- och factonlinesales-tabeller. Allt du gör här är att definiera kolumnnamn och datatyper och binda dem till platsen och formatet för Azure blob-lagringsfiler. Definitionen lagras i SQL Analytics-datalagret och data finns fortfarande i Azure Storage Blob.

Parametern **LOCATION** är mappen under rotmappen i Azure Storage Blob. Varje tabell finns i en annan mapp.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="load-the-data"></a>Läs in data
Det finns olika sätt att komma åt externa data.  Du kan fråga data direkt från de externa tabellerna, läsa in data i nya tabeller i informationslagret eller lägga till externa data i befintliga informationslagertabeller.  

###  <a name="create-a-new-schema"></a>Skapa ett nytt schema

CTAS skapar en ny tabell som innehåller data.  Skapa först ett schema för contoso-data.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Läsa in data i nya tabeller

Om du vill läsa in data från Azure blob storage i informationslagrets tabell använder du uttrycket [SKAPA TABELL AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) Inläsning med [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md) utnyttjar de starkt skrivna externa tabeller som du har skapat. Om du vill läsa in data i nya tabeller använder du en CTAS-sats per tabell. 
 
CTAS skapar en ny tabell och fyller den med resultatet av en select-sats. CTAS definierar den nya tabellen så att samma kolumner och datatyper har samma kolumner och datatyper som resultatet av select-satsen. Om du markerar alla kolumner från en extern tabell blir den nya tabellen en replik av kolumnerna och datatyperna i den externa tabellen.

I det här exemplet skapar vi både dimensionen och faktatabellen som hash-distribuerade tabeller. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Spåra belastningens förlopp

Du kan spåra förloppet för din belastning med hjälp av dynamiska hanteringsvyer (DMVs). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="optimize-columnstore-compression"></a>Optimera columnstore-komprimering

Som standard lagrar SQL Analytics-datalagret tabellen som ett klustrade columnstore-index. När en inläsning har slutförts kanske vissa datarader inte komprimeras till columnstore.  Det finns olika skäl till varför detta kan hända. Mer information finns i [Hantera columnstore-index](sql-data-warehouse-tables-index.md).

Om du vill optimera frågeprestanda och columnstore-komprimering efter en belastning återskapar du tabellen så att columnstore-indexet komprimerar alla rader. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Mer information om hur du underhåller columnstore-index finns i artikeln [hantera columnstore indexes.](sql-data-warehouse-tables-index.md)

## <a name="optimize-statistics"></a>Optimera statistik

Det är bäst att skapa statistik med en kolumn direkt efter en belastning. Om du vet att vissa kolumner inte kommer att finnas i frågepredikater kan du hoppa över att skapa statistik för dessa kolumner. Om du skapar statistik med en kolumn i varje kolumn kan det ta lång tid att återskapa all statistik. 

Om du bestämmer dig för att skapa statistik med en kolumn i `prc_sqldw_create_stats` varje kolumn kan du använda det lagrade procedurkodexemplet i [statistikartikeln.](sql-data-warehouse-tables-statistics.md)

Följande exempel är en bra utgångspunkt för att skapa statistik. Den skapar statistik med en kolumn för varje kolumn i dimensionstabellen och på varje kopplingskolumn i faktatabellerna. Du kan alltid lägga till statistik med en eller flera kolumner i andra faktatabellkolumner senare.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Prestation olåst!
Du har läst in offentliga data i ditt informationslager. Bra jobbat!

Du kan nu börja fråga tabellerna för att utforska dina data. Kör följande fråga för att ta reda på den totala försäljningen per varumärke:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Nästa steg
Om du vill läsa in hela datauppsättningen kör du exemplet [för att läsa in hela Contoso-butiksdatalagret](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) från Microsoft SQL Server-exempeldatabasen.
Fler utvecklingstips finns i [Designbeslut och kodningstekniker för datalager](sql-data-warehouse-overview-develop.md).
