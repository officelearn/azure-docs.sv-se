---
title: Skapa TABLE AS SELECT (CTAS) i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för kodning med instruktionen CREATE TABLE AS SELECT (CTAS) i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: dad0b1570f54cde1b1d474d8ebfc78f793724ef4
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247761"
---
# <a name="using-create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Med CREATE TABLE AS SELECT (CTAS) i Azure SQL Data Warehouse
Tips för kodning med CREATE TABLE AS SELECT (CTAS) T-SQL-instruktionen i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="what-is-create-table-as-select-ctas"></a>Vad är CREATE TABLE AS SELECT (CTAS)?

Den [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) eller CTAS-utdraget är en av de viktigaste T-SQL-funktionerna tillgängliga. Det är en parallell åtgärd som skapar en ny tabell baserat på resultatet av en SELECT-instruktion. CTASD är det enklaste och snabbaste sättet att skapa en kopia av en tabell. 

## <a name="selectinto-vs-ctas"></a>VÄLJ... I vs. CTAS
Du kan överväga att CTAS som en blixtsnabb debiteras version av den [Välj... I](/sql/t-sql/queries/select-into-clause-transact-sql) instruktionen.

Nedan visas ett exempel på ett enkelt SELECT... TILL:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

I föregående exempel `[dbo].[FactInternetSales_new]` skapas som ROUND_ROBIN distribuerad tabell med ett GRUPPERAT COLUMNSTORE-INDEX eftersom det här är standardinställningarna för tabell i Azure SQL Data Warehouse.

VÄLJ... TILL, men tillåter inte att du kan ändra metod för programvarudistribution eller Indextypen som en del av åtgärden. Det här är här CTAS kommer in.

Om du vill konvertera föregående exempel till CTAS är ganska enkel och tydlig:

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

Med CTAS ska du kunna ändra båda fördelningen av tabelldata, samt tabelltypen. 

> [!NOTE]
> Om du bara vill ändra index i din `CTAS` åtgärden och källtabellen är hash-distribueras sedan din `CTAS` åtgärden utförs bäst om du behåller samma kolumn och data distributionstypen. På så sätt undviker mellan distribution dataförflyttning under åtgärden som är mer effektivt.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Använda CTAS för att kopiera en tabell
Kanske en av de vanligaste användningsområdena för `CTAS` skapar en kopia av en tabell så att du kan ändra DDL. Om till exempel din tabell som ursprungligen skapades `ROUND_ROBIN` och nu vill ändra den till en tabell som distribueras på en kolumn, `CTAS` är hur du ändrar kolumnen distribution. `CTAS` kan också användas för att ändra typer av partitionering, indexering eller kolumn.

Anta att du har skapat den här tabellen med hjälp av standardtyp för distribution av `ROUND_ROBIN` distribueras eftersom ingen distributionskolumn har angetts i den `CREATE TABLE`.

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

Nu vill du skapa en ny kopia av den här tabellen med ett grupperat Columnstore-Index så att du kan dra nytta av prestanda för grupperade Columnstore-tabeller. Du vill distribuera den här tabellen på ProductKey eftersom du kommer kopplingar till den här kolumnen och vill undvika dataförflyttning under kopplingar till ProductKey. Till sist vill du också lägga till partitionering på OrderDateKey så att du snabbt kan ta bort gamla data genom att släppa gamla partitioner. Här är CTAS-utdraget som kopierar din gamla tabell till en ny tabell.

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

Slutligen kan du byta namn på dina tabeller för att växla i den nya tabellen och sedan ta bort din gamla registret.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [!NOTE]
> SQL Data Warehouse stöder inte än automatiskt skapande eller uppdatering av statistik.  För att få bästa möjliga prestanda från dina frågor, är det viktigt att statistik skapas på alla kolumner i alla tabeller efter den första inläsningen eller vid betydande dataändringar.  En detaljerad förklaring av statistik finns i avsnittet [Statistics] [Statistics] i ämnesgruppen utveckla.
> 
> 

## <a name="using-ctas-to-work-around-unsupported-features"></a>Använda CTAS för att undvika funktioner som inte stöds
CTAS kan också användas för att undvika ett antal stöds inte funktionerna i listan nedan. Detta kan ofta visar sig vara en win/win-situation som inte bara din kod är kompatibla men ofta körs snabbare på SQL Data Warehouse. Det här är ett resultat av dess fullständigt parallelliserad design. Scenarier som går att arbeta runt med CTAS är:

* ANSI kopplingar på uppdateringar
* ANSI kopplingar till borttagningar
* MERGE-instruktion

> [!NOTE]
> Försök att tänka ”CTAS första”. Om du tror att du kan lösa problem med att använda `CTAS` sedan som är vanligtvis det bästa sättet att allt – även om du skriver mer data som ett resultat.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI join ersättning för update-instruktioner
Du kanske du har en komplex uppdatering som ansluter till fler än två tabeller som tillsammans använder ANSI koppla syntax för att utföra UPDATE eller DELETE.

Föreställ dig var du tvungen att uppdatera den här tabellen:

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

Den ursprungliga frågan kanske har tittat ungefär så här:

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

Eftersom SQL Data Warehouse inte stöder ANSI kopplingar i den `FROM` -satsen för en `UPDATE` -instruktionen, du kan inte kopiera den här koden över utan att ändra något.

Du kan använda en kombination av en `CTAS` och en implicit koppling att ersätta den här koden:

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
Ibland den bästa metoden för att ta bort data är att använda `CTAS`. Välj de data som du vill behålla i stället för att helt enkelt ta bort data. Den här särskilt för `DELETE` instruktioner som använder ansi sammanbinder syntax eftersom SQL Data Warehouse inte stöder ANSI kopplingar i den `FROM` -satsen för en `DELETE` instruktionen.

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
Merge-instruktioner kan ersättas, minst i en del, med hjälp av CTAS. Du kan konsolidera INSERT och UPDATE till en enskild instruktion. Alla borttagna poster måste vara stängda i en andra instruktionen.

Följande är ett exempel på en UPSERT:

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
När du migrerar kod kanske du upptäcker att du kör i den här typen av mönster för kodning:

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

Du tror instinktivt bör du migrera den här koden till en CTAS och du skulle vara korrekt. Det finns dock en dold problem här.

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

Observera att kolumnen ”resultat” framåt innebär datavärden för typ och Nullbarheten för uttryck. Detta kan leda till mindre avvikelser i värden om du inte är försiktig.

Prova följande som ett exempel:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Värdet lagras i resultatet är olika. Felet blir ännu mer betydande beständiga värdet i resultatkolumnen används i andra uttryck.

![CTAS resultat](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Detta är särskilt viktigt för migrering av data. Även om den andra frågan är utan tvekan exaktare finns det ett problem. Data som skulle vara olika jämfört med källsystemet och som leder till frågor om integriteten i migreringen. Detta är en av dessa sällsynta fall där ”fel” svaret är faktiskt det rätt!

Det vi se den här diskrepans mellan de två resultaten beror till implicit typ omvandling. I det första exemplet definierar tabellen kolumndefinitionen. När rad infogas görs en implicit typkonvertering. I det andra exemplet finns det ingen implicit typkonvertering som uttrycket definierar datatypen för kolumnen. Observera också att kolumnen i det andra exemplet har definierats som en nullbar kolumn i det första exemplet har den inte. När tabellen har skapats i den första exemplet Nullbarheten definierades uttryckligen. I andra skulle meddelandet är bara kvar uttryck och som standard det här exemplet resultera i en NULL-definition.  

Lös problemen måste anges explicit typkonvertering och möjligt nullvärde i väljer delen av CTAS-utdraget. Du kan inte ange dessa egenskaper i Skapa tabell-del.

Exemplet nedan visar hur du åtgärdar koden:

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
> För Nullbarheten anges korrekt det är viktigt att använda ISNULL och inte COALESCE. COALESCE är inte en deterministisk funktion och resultatet av uttrycket blir så alltid kan ha värdet null. ISNULL är olika. Det är deterministisk. Därför när den andra delen av funktionen ISNULL är en konstant eller en literal sedan resultatvärdet blir NOT NULL.
> 
> 

Den här beskrivningen är inte bara användbar för att säkerställa integriteten hos dina beräkningar. Det är också viktigt för att växla till tabellen partition. Anta att du har den här tabellen definieras som din faktum:

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

Skapa din partitionerade datauppsättning som du kanske vill göra detta:

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

Frågan körs perfekt bra. Problemet är när du försöker utföra partitionsväxeln. Tabelldefinitionerna stämmer inte överens. Om du vill göra tabelldefinitionerna som matchar CTAS ska ändras.

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

Du kan se därför att typen konsekvens och underhålla Nullbarheten egenskaper på en CTAS är bra engineering bästa praxis. Det hjälper dig för att upprätthålla integriteten i beräkningarna och säkerställer också att växla partition är möjligt.

Finns det [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) dokumentation. Det är en av de viktigaste rapporterna i Azure SQL Data Warehouse. Kontrollera att du verkligen förstår.

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

