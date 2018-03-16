---
title: "Kom igång med U-SQL-katalogen | Microsoft Docs"
description: "Lär dig hur du använder U-SQL-katalogen för att dela koden och data."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: saveenr
ms.openlocfilehash: b39b5250cc042c393216784128ffc4e2f1288f04
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-u-sql-catalog"></a>Kom igång med U-SQL-katalogen

## <a name="create-a-tvf"></a>Skapa en Tabellvärdesfunktion

I tidigare U-SQL-skriptet upprepas användningen av EXTRAHERA att läsa från samma källfil. Du kan använda den U-SQL-Tabellvärdesfunktionen (Tabellvärdesfunktion), för att kapsla in data för senare användning.  

Följande skript skapar en Tabellvärdesfunktion kallas `Searchlog()` i standarddatabasen och -schemat:

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

Följande skript visar hur du använder Tabellvärdesfunktionen som har definierats i föregående skript:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SerachLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Skapa vyer

Om du har ett enda frågeuttryck i stället för en Tabellvärdesfunktion kan du använda en U-SQL-vy kapsla in uttrycket.

Följande skript skapar en vy som heter `SearchlogView` i standarddatabasen och -schemat:

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
Som med relationsdatabas tabeller med U-SQL kan du skapa en tabell med ett fördefinierat schema eller skapa en tabell som härleder schemat från frågan infogas som i tabell (även kallat CREATE TABLE AS SELECT eller CTAS).

Skapa en databas och två tabeller med följande skript:

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
Du kan fråga tabeller, till exempel de som skapats i skriptet tidigare på samma sätt som du frågar datafilerna. I stället för att skapa en raduppsättning med EXTRAHERA kan du nu referera till tabellens namn.

Ändra transform-skript som du har använt tidigare om du vill läsa från tabeller:

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
 >Du kan inte för närvarande kör en väljer i en tabell i samma skript som där tabellen har skapats.

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
