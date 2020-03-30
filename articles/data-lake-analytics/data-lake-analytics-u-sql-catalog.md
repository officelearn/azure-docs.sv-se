---
title: Använda U-SQL-katalogen i Azure Data Lake Analytics
description: Lär dig hur du använder U-SQL-katalogen för att dela kod och data. Skapa tabellvärdade funktioner, skapa vyer, skapa tabeller och fråga dem.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672851"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Komma igång med U-SQL-katalogen i Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Skapa en TVF

I det tidigare U-SQL-skriptet upprepade du användningen av EXTRACT för att läsa från samma källfil. Med funktionen U-SQL-tabellvärderad (TVF) kan du kapsla in data för framtida återanvändning.  

Följande skript skapar en TVF som anropas `Searchlog()` i standarddatabasen och schemat:

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

Följande skript visar hur du använder TVF som definierades i föregående skript:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Skapa vyer

Om du har ett enda frågeuttryck kan du i stället för en TVF använda en U-SQL VIEW för att kapsla in uttrycket.

I följande skript skapas `SearchlogView` en vy som anropas i standarddatabasen och standardschemat:

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

Följande skript visar hur den definierade vyn används:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Skapa tabeller
Precis som med relationsdatabastabeller kan du med U-SQL skapa en tabell med ett fördefinierat schema eller skapa en tabell som härleder schemat från frågan som fyller i tabellen (kallas även CREATE TABLE AS SELECT eller CTAS).

Skapa en databas och två tabeller med hjälp av följande skript:

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Frågetabeller
Du kan fråga tabeller, till exempel de som skapats i föregående skript, på samma sätt som du frågar efter datafilerna. I stället för att skapa en raduppsättning med hjälp av EXTRACT kan du nu referera till tabellnamnet.

Om du vill läsa från tabellerna ändrar du transformeringsskriptet som du använde tidigare:

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >För närvarande kan du inte köra en SELECT på en tabell i samma skript som den där du skapade tabellen.

## <a name="next-steps"></a>Efterföljande moment
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
