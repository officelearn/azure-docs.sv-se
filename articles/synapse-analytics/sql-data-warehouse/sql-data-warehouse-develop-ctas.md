---
title: SKAPA TABELL SOM SELECT (CTAS)
description: Förklaring och exempel på CREATE TABLE AS SELECT (CTAS) i Synapse SQL för att utveckla lösningar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 8e1b75dfc6a979956ff4a2868027bb769bf7c4ed
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633539"
---
# <a name="create-table-as-select-ctas"></a>SKAPA TABELL SOM SELECT (CTAS)

I den här artikeln beskrivs T-SQL-uttrycket SKAPA TABELL AS SELECT (CTAS) i Synapse SQL för att utveckla lösningar. Artikeln innehåller också kodexempel.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[Create Table AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) är en av de viktigaste T-SQL-funktionerna som finns tillgängliga. CTAS är en parallell åtgärd som skapar en ny tabell baserat på utdata från en SELECT-sats. CTAS är det enklaste och snabbaste sättet att skapa och infoga data i en tabell med ett enda kommando.

## <a name="selectinto-vs-ctas"></a>Välj... INTO vs CTAS

CTAS är en mer anpassningsbar version av [SELECT... INTO-uttalande.](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Följande är ett exempel på en enkel SELECT... I:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Välj... INTO tillåter inte att du ändrar vare sig distributionsmetoden eller indextypen som en del av åtgärden. Du `[dbo].[FactInternetSales_new]` skapar med hjälp av standarddistributionstypen för ROUND_ROBIN och standardtabellstrukturen för CLUSTERED COLUMNSTORE INDEX.

Med CTAS kan du däremot ange både fördelningen av tabelldata och tabellstrukturtypen. Så här konverterar du föregående exempel till CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Om du bara försöker ändra indexet i CTAS-åtgärden och källtabellen är hash-fördelad, underhåller du samma distributionskolumn och datatyp. På så sätt undviks datarörelser mellan distribution under åtgärden, vilket är effektivare.

## <a name="use-ctas-to-copy-a-table"></a>Använda CTAS för att kopiera en tabell

Kanske en av de vanligaste användningsområdena för CTAS är att skapa en kopia av en tabell för att ändra DDL. Anta att du ursprungligen skapade tabellen `ROUND_ROBIN`som och nu vill ändra den till en tabell som är fördelad i en kolumn. CTAS är hur du skulle ändra distributionskolumnen. Du kan också använda CTAS för att ändra partitionering, indexering eller kolumntyper.

Anta att du har skapat den här tabellen `ROUND_ROBIN`med standarddistributionstypen `CREATE TABLE`, inte ange en distributionskolumn i .

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
    CustomerPONumber nvarchar(25));
```

Nu vill du skapa en ny kopia `Clustered Columnstore Index`av den här tabellen, med en , så att du kan dra nytta av prestandan för grupperade Columnstore-tabeller. Du vill också distribuera `ProductKey`den här tabellen på , eftersom du förutser kopplingar i `ProductKey`den här kolumnen och vill undvika dataförflyttning under kopplingar på . Slutligen vill du också lägga till `OrderDateKey`partitionering på , så att du snabbt kan ta bort gamla data genom att släppa gamla partitioner. Här är CTAS-satsen, som kopierar din gamla tabell till en ny tabell.

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

Slutligen kan du byta namn på tabellerna, byta i det nya bordet och sedan släppa det gamla bordet.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Använda CTAS för att komma runt funktioner som inte stöds

Du kan också använda CTAS för att arbeta runt ett antal av de funktioner som inte stöds nedan. Den här metoden kan ofta vara till hjälp, eftersom inte bara din kod är kompatibel, men den körs ofta snabbare på Synapse SQL. Denna prestanda är ett resultat av dess helt parallelliserade design. Scenarier inkluderar:

* ANSI ANSLUTER PÅ UPDATEs
* ANSI JOINs på DELETEs
* KOPPLA sats

> [!TIP]
> Försök att tänka "CTAS först." Att lösa ett problem med hjälp av CTAS är i allmänhet ett bra tillvägagångssätt, även om du skriver mer data som ett resultat.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI-koppling ersättning för uppdateringssatser

Du kanske upptäcker att du har en komplex uppdatering. Uppdateringen sammanfogar mer än två tabeller tillsammans med ansi-kopplingssyntaxen för att utföra UPPDATERING eller TA BORT.

Tänk dig att du var tvungen att uppdatera den här tabellen:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Den ursprungliga frågan kan ha sett ut ungefär så här:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL stöder inte ANSI-kopplingar `FROM` i `UPDATE` satsen för ett uttryck, så du kan inte använda föregående exempel utan att ändra det.

Du kan använda en kombination av en CTAS och en implicit koppling för att ersätta föregående exempel:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI-koppling ersättning för borttagningssatser

Ibland är den bästa metoden för att ta `DELETE` bort data att använda CTAS, särskilt för satser som använder ANSI-kopplingssyntax. Detta beror på att Synapse SQL inte `FROM` stöder ANSI-kopplingar till satsen i ett `DELETE` uttryck. I stället för att ta bort data markerar du de data som du vill behålla.

Följande är ett exempel på `DELETE` en konverterad sats:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Ersätt kopplingssatser

Du kan ersätta kopplingssatser, åtminstone delvis, med hjälp av CTAS. Du kan `INSERT` kombinera `UPDATE` och till ett enda uttryck. Alla borttagna poster bör `SELECT` begränsas från satsen för att utelämna resultaten.

Följande exempel är `UPSERT`för en:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Tydligt tillståndsdatatyp och ogiltighet för utdata

När du migrerar kod kan du hitta du stöter på den här typen av kodningsmönster:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Du kanske tycker att du ska migrera den här koden till CTAS, och du skulle vara korrekt. Men det finns en dold fråga här.

Följande kod ger inte samma resultat:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Observera att kolumnen "resultat" för vidare datatypen och nullability-värdena för uttrycket. Om du bär datatypen framåt kan det leda till subtila avvikelser i värdena om du inte är försiktig.

Prova det här exemplet:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Värdet som lagras för resultatet är annorlunda. När det beständiga värdet i resultatkolumnen används i andra uttryck blir felet ännu mer betydande.

![Skärmbild av CTAS-resultat](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Detta är viktigt för datamigreringar. Även om den andra frågan är utan tvekan mer exakt, det finns ett problem. Uppgifterna skulle vara annorlunda jämfört med källsystemet, och det leder till integritetsfrågor i migreringen. Detta är ett av de sällsynta fall där "fel" svaret är faktiskt den rätta!

Anledningen till att vi ser en skillnad mellan de två resultaten beror på implicit typ gjutning. I det första exemplet definierar tabellen kolumndefinitionen. När raden infogas sker en implicit typkonvertering. I det andra exemplet finns det ingen implicit typkonvertering eftersom uttrycket definierar kolumnens datatyp.

Observera också att kolumnen i det andra exemplet har definierats som en NULLable kolumn, medan det i det första exemplet inte har. När tabellen skapades i det första exemplet definierades kolumnens nullability uttryckligen. I det andra exemplet lämnades det till uttrycket och som standard skulle det resultera i en NULL-definition.

För att lösa dessa problem måste du uttryckligen ange typkonvertering och nullability i SELECT-delen av CTAS-satsen. Du kan inte ange dessa egenskaper i SKAPA TABELL.
I följande exempel visas hur du åtgärdar koden:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Observera följande:

* Du kan använda CAST eller KONVERTERA.
* Använd ISNULL, inte KOLSCE, för att tvinga NULLability. Se följande anmärkning.
* ISNULL är den yttersta funktionen.
* Den andra delen av ISNULL är en konstant, 0.

> [!NOTE]
> För att nullability ska vara korrekt inställd, är det viktigt att använda ISNULL och inte coalesce. COALESCE är inte en deterministisk funktion, och så resultatet av uttrycket kommer alltid att vara NULLable. ISNULL är annorlunda. Det är deterministiskt. Därför, när den andra delen av ISNULL-funktionen är en konstant eller en bokstavlig, blir det resulterande värdet INTE NULL.

Att säkerställa integriteten för dina beräkningar är också viktigt för byte av tabellpartitioner. Föreställ dig att du har den här tabellen definierad som en faktatabell:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Beloppsfältet är dock ett beräknat uttryck. Det är inte en del av källdata.

Om du vill skapa partitionerad datauppsättning kanske du vill använda följande kod:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Frågan skulle fungera alldeles utmärkt. Problemet kommer när du försöker göra partitionsväxeln. Tabelldefinitionerna stämmer inte överens. Om du vill att tabelldefinitionerna ska matcha `ISNULL` ändrar du CTAS för att lägga till en funktion för att bevara kolumnens nullability-attribut.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Du kan se att typkonsekvens och underhåll av nullability-egenskaper på en CTAS är en metod för teknisk utveckling. Det bidrar till att upprätthålla integriteten i dina beräkningar och säkerställer också att partitionsväxling är möjlig.

CTAS är en av de viktigaste uttalandena i Synapse SQL. Se till att du förstår det ordentligt. Se [CTAS-dokumentationen](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [utvecklingsöversikten](sql-data-warehouse-overview-develop.md).
