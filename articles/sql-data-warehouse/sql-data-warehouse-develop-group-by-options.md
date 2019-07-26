---
title: Använda Group by-alternativ i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att implementera gruppera efter alternativ i Azure SQL Data Warehouse för att utveckla lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2f6614f32c31338c9cf4f00307c475db4e02f553
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479640"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Gruppera efter alternativ i SQL Data Warehouse
Tips för att implementera gruppera efter alternativ i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="what-does-group-by-do"></a>Vad sker gruppera efter?

[Group by](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL-satsen sammanställer data till en sammanfattande uppsättning rader. GROUP BY har vissa alternativ som SQL Data Warehouse inte stöder. Dessa alternativ har lösningar.

De här alternativen är

* Gruppera efter med sammanslagning
* GRUPPERADE UPPSÄTTNINGAR
* Gruppera efter med kub

## <a name="rollup-and-grouping-sets-options"></a>Alternativ för sammanfattning och gruppering av uppsättningar
Det enklaste alternativet är att använda UNION ALL i stället för att utföra sammanslagningen i stället för att förlita dig på den explicita syntaxen. Resultatet är exakt samma

I följande exempel används GROUP BY-instruktionen med alternativet samla in:
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Genom att använda ROLLUP begär föregående exempel följande agg regeringar:

* Land och region
* Country
* Total summa

Om du vill ersätta ROLLUP och returnera samma resultat kan du använda UNION alla och uttryckligen ange de nödvändiga agg regeringar:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Exempel principen gäller för att ersätta grupp uppsättningar. Du behöver bara skapa UNION alla avsnitt för de agg regerings nivåer som du vill se.

## <a name="cube-options"></a>Kubalternativ
Det är möjligt att skapa en grupp med hjälp av kub med hjälp av UNION ALL-metoden. Problemet är att koden snabbt kan bli besvärlig och svårhanterligt. Du kan använda den här mer avancerade metoden för att undvika detta.

Vi ska använda exemplet ovan.

Det första steget är att definiera ' Cube ' som definierar alla nivåer av agg regering som vi vill skapa. Det är viktigt att anteckna kors kopplingen för de två härledda tabellerna. Detta genererar alla nivåer för oss. Resten av koden är i själva verket för formatering.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Följande visar resultatet av CTAS:

![Gruppera efter kub](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Det andra steget är att ange en mål tabell för att lagra interimistiska resultat:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Det tredje steget är att loopa över vår kub av kolumner som utför aggregation. Frågan körs en gång för varje rad i #Cube temporär tabell och lagrar resultaten i tabellen #Results temporära

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Slutligen kan du returnera resultaten genom att bara läsa från #Results temporär tabell

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Genom att dela upp koden i avsnitt och generera en loop-konstruktion blir koden mer hanterbar och hanterbar.

## <a name="next-steps"></a>Nästa steg
Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).

