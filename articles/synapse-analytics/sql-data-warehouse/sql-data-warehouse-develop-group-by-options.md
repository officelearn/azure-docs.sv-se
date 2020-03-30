---
title: Använda grupp efter alternativ
description: Tips för att implementera grupp efter alternativ i Azure SQL Data Warehouse för att utveckla lösningar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f77445e80e701053b7fbfa1aa559248cf505353c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350514"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Gruppera efter alternativ i SQL Data Warehouse
Tips för att implementera grupp efter alternativ i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="what-does-group-by-do"></a>Vad gör GROUP BY?

[Satsgrupp av](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL-satsen sammanställer data till en sammanfattningsuppsättning rader. GROUP BY har några alternativ som SQL Data Warehouse inte stöder. De här alternativen har lösningar.

Dessa alternativ är

* GRUPPERA EFTER MED SAMMANSLAGNING
* GRUPPERA UPPSÄTTNINGAR
* GRUPPERA EFTER MED KUB

## <a name="rollup-and-grouping-sets-options"></a>Alternativ för sammanslagning och gruppering anger
Det enklaste alternativet här är att använda UNION ALL istället för att utföra sammanslagningen i stället för att förlita sig på den explicita syntaxen. Resultatet är exakt samma

I följande exempel med sats av GRUPP FÖR med alternativet SAMLAD:
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

Genom att använda samlad, föregående exempel begär följande aggregeringar:

* Land och region
* Land/region
* Totalsumma

Om du vill ersätta samlad och returnera samma resultat kan du använda UNION ALL och uttryckligen ange de aggregeringar som krävs:

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

Om du vill ersätta GRUPPERINGSUPPSÄTTNINGAR gäller exempelprincipen. Du behöver bara skapa UNION ALLA avsnitt för de aggregeringsnivåer som du vill se.

## <a name="cube-options"></a>Alternativ för kub
Det är möjligt att skapa en GRUPP MED KUB med hjälp av union all-metoden. Problemet är att koden snabbt kan bli besvärlig och svårhanterlig. För att minska detta kan du använda den här mer avancerade metoden.

Låt oss använda exemplet ovan.

Det första steget är att definiera "kuben" som definierar alla nivåer av aggregering som vi vill skapa. Det är viktigt att notera CROSS JOIN av de två härledda tabellerna. Detta genererar alla nivåer för oss. Resten av koden är verkligen där för formatering.

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

Följande visar resultaten av CTAS:

![Gruppera efter kub](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Det andra steget är att ange en måltabell för att lagra delårsresultat:

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

Det tredje steget är att loopa över vår kub av kolumner som utför aggregering. Frågan körs en gång för varje rad i #Cube temporära tabellen och lagrar resultaten i #Results temporärtabellen

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

Slutligen kan du returnera resultaten genom att helt enkelt läsa från #Results temporära tabellen

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Genom att dela upp koden i sektioner och generera en looping konstruktion, blir koden mer hanterbar och underhållsbar.

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

