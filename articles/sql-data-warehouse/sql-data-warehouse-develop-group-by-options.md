---
title: Med hjälp av grupp med alternativen i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för gruppen av alternativen i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0548983df23b158385783ac777b23268b5ac7d01
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526054"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Gruppera efter alternativ i SQL Data Warehouse
Tips för gruppen av alternativen i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="what-does-group-by-do"></a>Vad är GROUP BY?

Den [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL-satsen samlar in data till en sammanfattande uppsättning rader. GRUPPEN har vissa alternativ som inte har stöd för SQL Data Warehouse. Dessa alternativ har olika lösningar.

Dessa alternativ är

* GROUP BY med Samlad
* GROUPING SETS
* GROUP BY med kub

## <a name="rollup-and-grouping-sets-options"></a>Anger alternativ för insamling och gruppering
Det enklaste alternativet är att använda UNION ALL i stället för att utföra samlade i stället för att förlita dig på explicit syntax. Resultatet är exakt samma

I följande exempel med GROUP BY-instruktionen med alternativet samlad:
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

Med hjälp av insamling av begäranden i föregående exempel följande aggregeringar:

* Land och Region
* Land/region
* Totalsumma

Du kan använda UNION ALL och uttryckligen ange aggregeringar krävs om du vill ersätta samlad och ger samma resultat:

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

Om du vill ersätta GROUPING SETS gäller exempel principen. Behöver du bara skapa UNION ALL avsnitt för aggregering-nivåer som du vill se.

## <a name="cube-options"></a>Kubalternativ för
Det är möjligt att skapa en grupp av med kub med hjälp av UNION ALL-metoden. Problemet är att koden kan snabbt bli besvärlig och svårhanterliga. Om du vill åtgärda det, kan du använda den mer avancerade metod.

Nu ska vi använda exemplet ovan.

Det första steget är att definiera 'kuben' som definierar alla nivåer av aggregation som vi vill skapa. Det är viktigt att notera CROSS JOIN av två härledda tabeller. Detta genererar alla nivåer för oss. Resten av koden är verkligen det för formatering.

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

Nedan visas resultatet av CTAS:

![Gruppera efter kuben](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Det andra steget är att ange en måltabell för att lagra tillfälliga resultat:

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

Det tredje steget är att köras i slinga över våra kub med kolumner som utför aggregering. Frågan ska köras en gång för varje rad i den temporära tabellen #Cube och spara resultatet i den temporära tabellen #Results

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

Till sist ska du gå tillbaka resultaten genom att bara läsa från den temporära tabellen #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Genom att dela koden upp i avsnitt och genererar en slinga konstruktion, blir koden mer användarvänlig och hanterbar.

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

