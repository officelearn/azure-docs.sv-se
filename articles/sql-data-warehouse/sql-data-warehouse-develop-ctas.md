---
title: Skapa TABLE AS SELECT (CTAS) i Azure SQL Data Warehouse | Microsoft Docs
description: Förklaring och exempel på instruktionen CREATE TABLE AS SELECT (CTAS) i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: c8e9f3ccdfaee64f75443f6a4eb89a3df7c48b0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403792"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Skapa TABLE AS SELECT (CTAS) i Azure SQL Data Warehouse

Den här artikeln förklarar CREATE TABLE AS SELECT (CTAS) T-SQL-instruktionen i Azure SQL Data Warehouse för utveckling av lösningar. Artikeln innehåller även kodexempel.

## <a name="create-table-as-select"></a>SKAPA TABLE AS SELECT

Den [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS)-instruktionen är en av de viktigaste T-SQL-funktionerna tillgängliga. CTAS är en parallell åtgärd som skapar en ny tabell baserat på resultatet av en SELECT-instruktion. CTAS är det enklaste och snabbaste sättet att skapa och infoga data i en tabell med ett enda kommando.

## <a name="selectinto-vs-ctas"></a>VÄLJ... I vs. CTAS

CTAS är en mer anpassningsbar version av den [Välj... I](/sql/t-sql/queries/select-into-clause-transact-sql) instruktionen.

Följande är ett exempel på ett enkelt SELECT... TILL:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

VÄLJ... I låter dig inte ändra metod för programvarudistribution eller Indextypen som en del av åtgärden. Du skapar `[dbo].[FactInternetSales_new]` med hjälp av standardtyp för distribution av ROUND_ROBIN och standard tabellstrukturen KLUSTRADE COLUMNSTORE-index.

Med CTAS, å andra sidan kan du ange båda fördelningen av tabelldata, samt struktur tabelltyp. Så här konverterar föregående exempel till CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Om du försöker bara ändra index i CTAS-åtgärd och källtabellen är hash-distribueras, behålla samma kolumn och data distributionstypen. På så sätt undviker dataförflyttning mellan distribution under åtgärden, vilket är mer effektivt.

## <a name="use-ctas-to-copy-a-table"></a>Använda CTAS för att kopiera en tabell

Kanske en av de vanligaste sätten att CTAS skapar en kopia av en tabell för att kunna ändra DDL. Låt oss säga att du skapade tabellen som `ROUND_ROBIN`, och nu vill ändra den till en tabell som distribueras på en kolumn. CTAS är hur du ändrar kolumnen distribution. Du kan också använda CTAS för att ändra typer av partitionering, indexering eller kolumn.

Låt oss säga att du skapade den här tabellen med hjälp av standardtyp för distribution av `ROUND_ROBIN`, inte att ange en distributionskolumn i den `CREATE TABLE`.

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

Nu vill du skapa en ny kopia av den här tabellen med en `Clustered Columnstore Index`, så du kan dra nytta av prestanda för grupperade Columnstore-tabeller. Du vill distribuera den här tabellen på `ProductKey`, eftersom du ser fram emot kopplingar till den här kolumnen och vill undvika dataförflyttning under kopplingar på `ProductKey`. Slutligen kan du också vill lägga till partitionering på `OrderDateKey`, så du kan snabbt ta bort gamla data genom att släppa gamla partitioner. Här är den CTAS-instruktionen, som kopierar din gamla tabell till en ny tabell.

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

Slutligen kan du byta namn på dina tabeller, för att växla i den nya tabellen och sedan ta bort din gamla registret.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Använda CTAS för att undvika funktioner som inte stöds

Du kan också använda CTAS för att undvika ett antal stöds inte funktionerna i listan nedan. Den här metoden kan ofta vara användbart, eftersom inte bara din kod blir kompatibel, men ofta körs snabbare på SQL Data Warehouse. Den här prestanda beror på dess fullständigt parallelliserad design. Scenarier är:

* ANSI kopplingar på uppdateringar
* ANSI kopplingar till borttagningar
* MERGE-instruktion

> [!TIP]
> Försök att tänka ”CTAS första”. Lösa ett problem med hjälp av CTAS är vanligtvis en bra metod, även om du vill skriva mer data som ett resultat.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI join ersättning för update-instruktioner

Du kanske upptäcker att du har en komplex uppdatering. Uppdateringen kopplar ihop mer än två tabeller genom att använda ANSI join-syntax för att utföra UPDATE eller DELETE.

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

Den ursprungliga frågan kanske har tittat något som liknar det här exemplet:

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

SQL Data Warehouse stöder inte kopplingar av ANSI i den `FROM` -satsen för en `UPDATE` -instruktionen, så du inte kan använda det tidigare exemplet utan att ändra den.

Du kan använda en kombination av en CTAS och en implicit koppling för att ersätta det tidigare exemplet:

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

Ibland den bästa metoden för att ta bort data är att använda CTAS, särskilt för `DELETE` instruktioner som använder ANSI join syntax. Detta beror på att SQL Data Warehouse inte stöder kopplingar av ANSI i den `FROM` -satsen för en `DELETE` instruktionen. Välj de data som du vill behålla i stället för att ta bort data.

Följande är ett exempel på en konverterade `DELETE` instruktionen:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
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

Du kan ersätta merge-instruktioner, minst i en del, med hjälp av CTAS. Du kan kombinera den `INSERT` och `UPDATE` i en enskild instruktion. Alla borttagna poster ska hindras från att den `SELECT` -uttryck som ska uteslutas från resultaten.

Följande exempel gäller en `UPSERT`:

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
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Uttryckligen ange datatyp och Nullbarheten för utdata

När du migrerar kod, kanske du upptäcker att du kör i den här typen av mönster för kodning:

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

Du bör överväga att du ska migrera den här koden till CTAS och du skulle vara korrekt. Det finns dock en dold problem här.

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

Observera att kolumnen ”resultat” framåt innebär datavärden för typ och Nullbarheten för uttryck. Körts data kan typ framåt leda till mindre avvikelser i värden om du inte är försiktig.

Testa det här exemplet:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Värdet lagras i resultatet är olika. Felet blir ännu viktigare när beständiga värdet i resultatkolumnen används i andra uttryck.

![Skärmbild av CTAS resultat](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Detta är viktigt för migrering av data. Även om den andra frågan är utan tvekan exaktare, finns det ett problem. Data som skulle vara olika jämfört med källsystemet och som leder till frågor om integriteten i migreringen. Detta är en av dessa sällsynta fall där ”fel” svaret är faktiskt det rätt!

Det ser vi en diskrepans mellan de två resultaten beror på grund av implicit typ omvandling. I det första exemplet definierar tabellen kolumndefinitionen. När raden infogas, sker en implicit typkonvertering. I det andra exemplet finns det ingen implicit typkonvertering som uttrycket definierar datatypen för kolumnen.

Observera också att kolumnen i det andra exemplet har definierats som en kolumn som kan ha värdet null, men i det första exemplet inte har den. När tabellen skapades i det första exemplet definierades uttryckligen Nullbarhet. I det andra exemplet den lämnades uttryck och som standard skulle resultera i en NULL-definition.

Lös problemen, måste du uttryckligen ange typkonvertering och möjligt nullvärde i väljer delen av CTAS-utdraget. Du kan inte ange dessa egenskaper i CREATE TABLE.
I följande exempel visar hur du åtgärdar koden:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Observera följande:

* Du kan använda CAST eller konvertera.
* Använd ISNULL, inte COALESCE om du vill framtvinga Nullbarheten. Se följande kommentaren.
* ISNULL är funktionen yttersta.
* Den andra delen av ISNULL är en konstant, 0.

> [!NOTE]
> Det är viktigt att använda ISNULL och inte COALESCE för Nullbarheten anges korrekt. COALESCE är inte en deterministisk funktion och resultatet av uttrycket blir så alltid kan ha värdet null. ISNULL är olika. Det är deterministisk. När den andra delen av funktionen ISNULL är en konstant eller en litteral, resultatvärdet kommer därför inte NULL.

Säkerställer integriteten för dina beräkningar är också viktigt för att växla till tabellen partition. Anta att du har den här tabellen definieras som en faktatabell:

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

Beloppsfältet är dock ett beräknat uttryck. Det är inte en del av datakällan.

För att skapa din partitionerade datauppsättning, kanske du vill använda följande kod:

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

Frågan körs utmärkt. Problemet är när du försöker göra partitionsväxeln. Tabelldefinitionerna matchar inte. Att göra tabelldefinitionerna matchar, ändra CTAS att lägga till en `ISNULL` funktionen för att bevara den kolumnen nullbarhetsattribut.

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

Du kan se att typen konsekvens och underhålla Nullbarheten egenskaper på en CTAS är en engineering bästa praxis. Det hjälper dig för att upprätthålla integriteten i beräkningarna och säkerställer också att växla partition är möjligt.

CTAS är en av de viktigaste rapporterna i SQL Data Warehouse. Kontrollera att du verkligen förstår. Se den [CTAS dokumentation](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips, se den [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

