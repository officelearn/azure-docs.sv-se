---
title: Kom igång med U-SQL-katalogen i Azure Data Lake Analytics
description: Lär dig hur du använder U-SQL-katalogen för att dela kod och data.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: a6faa7037ccbacc0547401dd52bb3b19abd1c474
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401613"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Kom igång med U-SQL-katalogen i Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Skapa en Tabellvärdesfunktion

I föregående U-SQL-skript upprepas användningen av EXTRAHERA att läsa från samma källfil. Du kan använda den U-SQL Tabellvärdesfunktionen (TVF), för att kapsla in data för senare användning.  

Följande skript skapar en Tabellvärdesfunktion kallas `Searchlog()` i standarddatabas och schema:

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

Följande skript visar hur du använder Tabellvärdesfunktionen som definierades i föregående skript:

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

Om du har ett enda frågeuttryck, i stället för en Tabellvärdesfunktion kan du använda en U-SQL-vy att kapsla in uttrycket.

Följande skript skapar en vy som heter `SearchlogView` i standarddatabas och schema:

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

Följande skript visar hur du använder den definierade vy:

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
Som med relationsdatabas tabeller med U-SQL kan du skapa en tabell med ett fördefinierat schema eller skapa en tabell som härleder scheman från frågan infogas som i tabell (även kallat CREATE TABLE AS SELECT eller CTAS).

Skapa en databas och två tabeller genom att använda följande skript:

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
Du kan fråga tabeller, till exempel de som används i föregående skript på samma sätt som du fråga datafilerna. Istället för att skapa en raduppsättning med hjälp av EXTRAHERA nu ser du tabellnamnet.

Ändra transform-skriptet som du använde tidigare för att läsa från tabeller:

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
 >Du kan inte för närvarande kan köra en väljer på en tabell i samma skript som den där du skapade tabellen.

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
