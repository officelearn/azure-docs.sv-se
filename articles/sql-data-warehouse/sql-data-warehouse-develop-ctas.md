---
title: "Skapa tabell som Välj (CTAS) i SQL Data Warehouse | Microsoft Docs"
description: "Tips för att koda med tabellen skapa som select (CTAS)-instruktionen i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 68ac9a94-09f9-424b-b536-06a125a653bd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: a885ba4f455fecd158696faaee38c83c1e4ec0bf
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Skapa en tabell som Select (CTAS) i SQL Data Warehouse
Skapa tabell som markerar eller `CTAS` är en av de viktigaste T-SQL-funktionerna tillgängliga. Det är en helt parallelized åtgärd som skapar en ny tabell baserat på resultatet av en SELECT-instruktion. `CTAS`är det enklaste och snabbaste sättet att skapa en kopia av en tabell. Det här dokumentet innehåller både exempel och bästa praxis för `CTAS`.

## <a name="selectinto-vs-ctas"></a>VÄLJ... I vs. CTAS
Du kan överväga att `CTAS` som en mycket debiteras version av `SELECT..INTO`.

Nedan visas ett exempel på en enkel `SELECT..INTO` instruktionen:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

I exemplet ovan `[dbo].[FactInternetSales_new]` skulle skapas som ROUND_ROBIN distribuerade tabell med ett GRUPPERAT COLUMNSTORE-INDEX på den som dessa standardinställningar tabell i Azure SQL Data Warehouse.

`SELECT..INTO`dock tillåter inte att du kan ändra metoden distribution eller Indextypen som en del av åtgärden. Det är där `CTAS` kommer in.

Konvertera ovan för att `CTAS` är ganska enkel:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

Med `CTAS` kan du ändra både fördelningen av tabelldata samt tabelltypen. 

> [!NOTE]
> Om du bara vill ändra index i din `CTAS` åtgärden och källtabellen är hash distribueras sedan din `CTAS` åtgärden fungerar bäst om du behåller samma kolumn- och distributionstypen. På så sätt undviker mellan distribution dataflyttning under åtgärden, vilket är mer effektivt.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Använda CTAS för att kopiera en tabell
Kanske en av de vanligaste använder av `CTAS` skapar en kopia av en tabell, så att du kan ändra DDL. Om till exempel din tabell som ursprungligen skapades `ROUND_ROBIN` och nu vill ändra den till en tabell som distribueras på en kolumn, `CTAS` är hur du ändrar kolumnen distribution. `CTAS`kan också användas för att ändra partitionering, indexering eller kolumn typer.

Anta att du har skapat den här tabellen med standardtypen för distribution av `ROUND_ROBIN` distribueras eftersom ingen distribution kolumn har angetts i den `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Nu vill du skapa en ny kopia av den här tabellen med ett grupperat Columnstore-Index så att du kan dra nytta av grupperade Columnstore-tabeller. Du vill distribuera den här tabellen på ProductKey eftersom du förutser kopplingar på den här kolumnen och vill undvika dataflyttning under kopplingar på ProductKey. Till sist också du lägga till partitionering på OrderDateKey så att du snabbt kan ta bort gamla data genom att släppa gamla partitioner. Det här är den CTAS-instruktionen som kopierar din gamla tabell till en ny tabell.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Slutligen kan du byta namn på tabellerna för att växla i den nya tabellen och ta bort din gamla registret.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [!NOTE]
> SQL Data Warehouse stöder inte än automatiskt skapande eller uppdatering av statistik.  För att få bästa möjliga prestanda från dina frågor, är det viktigt att statistik skapas på alla kolumner i alla tabeller efter den första inläsningen eller vid betydande dataändringar.  En detaljerad förklaring av statistik finns i ämnet [Statistik][Statistics] i ämnesgruppen Utveckla.
> 
> 

## <a name="using-ctas-to-work-around-unsupported-features"></a>Använda CTAS för att undvika funktioner som inte stöds
`CTAS`kan också användas för att undvika ett antal stöds inte funktionerna i listan nedan. Detta kan ofta visar sig vara en win/win-situation som inte bara din kod blir kompatibla men det ofta körs snabbare i SQL Data Warehouse. Detta är på grund av dess fullständigt parallelized design. Scenarier som fungerade runt med CTAS är:

* ANSI kopplingar uppdateringar
* ANSI-kopplingar på borttagningar
* MERGE-instruktion

> [!NOTE]
> Försök att tänka ”CTAS första”. Om du tror att du kan lösa ett problem med `CTAS` sedan som är vanligtvis det bästa sättet att närma sig den – även om du skriver mer data som ett resultat.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI join ersättning för update-instruktioner
Du kanske du har en komplex uppdatering som ansluter till fler än två tabeller tillsammans använder ANSI koppla syntax för att utföra UPDATE- eller DELETE.

Anta att du var tvungen att uppdatera den här tabellen:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Den ursprungliga frågan kanske har såg ut ungefär så här:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Eftersom SQL Data Warehouse inte stöder ANSI kopplingar i den `FROM` -satsen i en `UPDATE` -instruktionen, du kan inte kopiera den här koden över utan att ändra något.

Du kan använda en kombination av en `CTAS` och en indirekt koppling att ersätta den här koden:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI join ersättning för delete-instruktioner
Ibland den bästa metoden för att ta bort data är att använda `CTAS`. Välj de data som du vill behålla i stället för att bara ta bort data. Den här särskilt för `DELETE` som använder ansi som sammanbinder syntax eftersom SQL Data Warehouse inte stöder ANSI kopplingar i den `FROM` -satsen i en `DELETE` instruktion.

Ett exempel på en konverterade DELETE-instruktion finns nedan:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Ersätt merge-instruktioner
Merge-instruktioner kan ersättas, minst del, med hjälp av `CTAS`. Du kan konsolidera den `INSERT` och `UPDATE` i en enskild instruktion. Alla borttagna poster måste vara stängda i ett andra uttryck.

Ett exempel på en `UPSERT` finns nedan:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>CTAS rekommendation: uttryckligen ange datatyp och Nullbarheten för utdata
När du migrerar kod kanske körs i den här typen av kodning mönster:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Du kanske instinktivt tror du bör migrera koden till en CTAS och du skulle vara korrekt. Det finns emellertid ett dolt problem.

Följande kod ger inte samma resultat:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Observera att kolumnen ”resultat” vidarebefordra innebär datavärden för typ och Nullbarheten för uttrycket. Detta kan leda till diskret avvikelser i värden om du inte är försiktig.

Försök med följande exempel:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Det värde som lagras för resultatet är olika. Det beständiga värdet i resultatkolumnen används i andra uttryck blir felet ännu större.

![][1]

Detta är särskilt viktigt för migrering av data. Även om den andra frågan tvekan exaktare finns problemet. Data kan vara olika jämfört med källsystemet och som leder till frågor för migreringen. Detta är en av de sällsynta fall där ”fel” svaret är verkligen det högra!

Det ser vi den här skillnader mellan de två resultaten beror till implicit typ omvandling. I det första exemplet definierar tabellen kolumndefinitionen. En implicit typkonvertering inträffar när raden infogas. I det andra exemplet finns det ingen implicit typkonvertering som uttrycket definierar datatypen för kolumnen. Observera också att kolumnen i det andra exemplet har definierats som en nullbar kolumn i det första exemplet har den inte. När tabellen har skapats i den första exemplet Nullbarheten definierades explicit. I andra resulterar exempel meddelandet är bara kvar i uttrycket och som standard detta i en NULL-definition.  

För att lösa dessa problem måste anges explicit typkonvertering och Nullbarheten i den `SELECT` del av den `CTAS` instruktionen. Du kan inte ange dessa egenskaper i create table-delen.

Exemplet nedan visar hur du löser koden:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Observera följande:

* CAST eller CONVERT kunde har använts
* ISNULL används för att tvinga Nullbarheten inte COALESCE
* ISNULL är funktionen yttersta
* Den andra delen av ISNULL är en konstant d.v.s. 0

> [!NOTE]
> För Nullbarheten anges korrekt det är mycket viktigt att `ISNULL` och inte `COALESCE`. `COALESCE`är inte en deterministisk funktion så att resultatet av uttrycket kommer alltid att kan ha värdet null. `ISNULL`är olika. Det är deterministisk. Därför när den andra delen av den `ISNULL` funktionen är en konstant eller en litteral sedan resultatvärdet blir NOT NULL.
> 
> 

Den här beskrivningen är inte bara användbar för att säkerställa integriteten hos dina beräkningar. Det är också viktigt för växling av partition i tabellen. Anta att du har den här tabellen definieras som din faktum:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Värdefältet är dock ett beräknat uttryck inte är en del av datakällan.

Om du vill skapa partitionerade datamängden kanske du vill göra detta:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Frågan körs perfekt bra. Problemet kommer när du försöker utföra partitionsväxeln. Tabelldefinitionerna stämmer inte överens. Om du vill göra tabelldefinitionerna matchar CTAS ska ändras.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Du kan se därför att typen konsekvens och underhålla Nullbarheten egenskaper för en CTAS är det engineering bästa praxis. Det hjälper dig för att upprätthålla integriteten i beräkningarna och säkerställer också att växla partition är möjligt.

Mer information om hur du använder finns i MSDN [CTAS][CTAS]. Det är en av de viktigaste rapporterna i Azure SQL Data Warehouse. Kontrollera att du verkligen förstår.

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt][development overview].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
