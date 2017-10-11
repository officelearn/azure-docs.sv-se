---
title: Gruppera efter alternativ i SQL Data Warehouse | Microsoft Docs
description: "Tips för gruppen av alternativen i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f95a1e43-768f-4b7b-8a10-8a0509d0c871
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: da71cb834c13da5d0f5690f471efc6c696163f30
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Gruppera efter alternativ i SQL Data Warehouse
Den [GROUP BY] [ GROUP BY] satsen används för att samla in data till en sammanfattande uppsättning rader. Det finns även några alternativ utökar dess funktioner som behöver bearbetas runt eftersom de inte direkt stöds av Azure SQL Data Warehouse.

Dessa alternativ är

* GROUP BY med Samlad
* GROUPING SETS
* GROUP BY med kub

## <a name="rollup-and-grouping-sets-options"></a>Anger alternativ för insamling och gruppering
Det enklaste alternativet är att använda `UNION ALL` i stället för samlade i stället för att förlita dig på explicit syntax. Resultatet är exakt samma

Nedan visas ett exempel på en grupp med hjälp av instruktionen den `ROLLUP` alternativ:

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

Vi har begärt följande aggregeringar med hjälp av samlad:

* Land och Region
* Land/region
* Totalsumma

Ersätt detta behöver du använda `UNION ALL`; ange aggregeringar måste uttryckligen returnera samma resultat:

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

För GROUPING SETS alla vi behöver göra är antar samma huvudkonto men bara skapa UNION ALL avsnitt för aggregering nivåerna vi vill se

## <a name="cube-options"></a>Kubalternativ för
Det är möjligt att skapa en grupp av med kub med hjälp av UNION ALL-metoden. Problemet är att koden kan snabbt bli besvärlig och svårhanterliga. Om du vill undvika detta kan du använda den mer avancerade metod.

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

Resultaten av CTAS visas nedan:

![][1]

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

Till sist kan vi returnera resultat genom att bara läsa från den temporära tabellen #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Genom att dela koden upp i avsnitt och genererar en slinga konstruktion blir koden mer användarvänlig och hanterbar.

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt][development overview].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
