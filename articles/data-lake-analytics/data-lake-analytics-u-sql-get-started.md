---
title: "Kom igång med U-SQL-språket | Microsoft Docs"
description: "Lär dig grunderna om U-SQL-språket."
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
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 38c4e1b9bd24ef0b8a81f6154620f3f98d3b5ac1
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-u-sql"></a>Kom igång med U-SQL
U-SQL är ett språk som kombinerar deklarativ SQL med tvingande C# för att du ska bearbeta data i alla skalor. Via skalbara, distribuerade frågan möjligheterna för U-SQL, kan du effektivt analysera data i relationella butiker, till exempel Azure SQL Database. Du kan bearbeta Ostrukturerade data genom att använda schemat vid läsning och lägga till egen kod och UDF: er med U-SQL. Dessutom innehåller U-SQL utökningsbarhet som ger detaljerad kontroll över hur du utför i större skala. 

## <a name="learning-resources"></a>Utbildningsresurser

* Den [U-SQL-kursen](http://aka.ms/usqltutorial) innehåller en guidad genomgång för de flesta av U-SQL-språket. Det här dokumentet bör läsa för alla utvecklare som vill lära dig U-SQL.
* Detaljerad information om den **U-SQL-syntaxen för anspråksregelspråket**, finns det [U-SQL-Språkreferens](http://go.microsoft.com/fwlink/p/?LinkId=691348).
* Att förstå den **U-SQL-designfilosofin**, bloggposten Visual Studio [introduktion till U-SQL – ett språk som är stort databearbetning enkelt](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Krav

Innan du går igenom U-SQL-exemplen i det här dokumentet, läsa och slutföra [Självstudier: utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Den här kursen beskrivs säkerhetsnivån med hjälp av U-SQL med Azure Data Lake-verktyg för Visual Studio.

## <a name="your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande U-SQL-skriptet är enkel och låt oss se många aspekter U-SQL-språket.

```
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

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Det här skriptet har inte några steg för omvandling. Det läser från källfilen kallas `SearchLog.tsv`schematizes den och skriver raduppsättningen tillbaka till en fil med namnet SearchLog-första-u-sql.csv.

Observera data frågetecken skriver i den `Duration` fältet. Det innebär att den `Duration` fältet kan vara null.

### <a name="key-concepts"></a>Viktiga begrepp
* **Raduppsättningen variabler**: varje frågeuttryck som producerar en raduppsättning kan tilldelas till en variabel. U-SQL följer T-SQL variabeln namngivningsmönstret (`@searchlog`, till exempel) i skriptet.
* Den **EXTRAHERA** nyckelordet läser data från en fil och definierar schemat vid läsning. `Extractors.Tsv`är en inbyggd U-SQL-extraktor för fliken värden filer. Du kan utveckla anpassade juicepressar.
* Den **utdata** skriver data från en raduppsättning till en fil. `Outputters.Csv()`är en inbyggd U-SQL-outputter att skapa en CSV-värdefil. Du kan utveckla anpassade outputters.

### <a name="file-paths"></a>Sökvägar

EXTRAHERA och utdata uttryck använder sökvägar. Sökvägar kan vara absolut eller relativ:

Följande absoluta sökvägen refererar till en fil i ett Data Lake Store med namnet `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Den här följande sökväg börjar med `"/"`. Den refererar till en fil i Data Lake Store-standardkontot:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Använd skalbara variabler

Du kan använda skalbara variabler samt för att underlätta skript-underhåll. Tidigare U-SQL-skriptet kan också anges som:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformera raduppsättningar

Använd **Välj** att omvandla raduppsättningar:

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

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE-satsen använder en [C# booleskt uttryck](https://msdn.microsoft.com/library/6a71f45d.aspx). Du kan använda uttryck-språket C# för att göra egna uttryck och funktioner. Du kan även utföra mer komplexa filtrering genom att kombinera dem med logiska konjunktioner (och) och disjunctions (ORs).

Följande skript använder metoden DateTime.Parse() och en tillsammans.

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

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >Den andra frågan körs på resultatet av raduppsättningen första som skapar en kombination av två filter. Du kan också använda ett variabelnamn och namnen är begränsade lexically.

## <a name="aggregate-rowsets"></a>Sammanställd raduppsättningar
U-SQL ger dig den välkända ORDER BY, GROUP BY och aggregeringar.

Följande fråga hittar den totala varaktigheten per region och visar sedan upp fem varaktighet i ordning.

U-SQL-raduppsättningar bevaras inte deras inbördes ordning för nästa fråga. Därför om du vill ordna utdata, måste du lägga till ORDER BY i utdata-instruktion:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

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

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U-SQL ORDER BY-satsen kräver att du använder FETCH-sats i en SELECT-uttryck.

MED U-SQL-instruktion kan användas för att begränsa resultatet till grupper som uppfyller villkoret HAVING:

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

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Avancerade aggregering scenarier finns i referensdokumentationen för U-SQL-för [sammanställa, analytiska och referera till funktioner](https://msdn.microsoft.com/en-us/library/azure/mt621335.aspx)

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
