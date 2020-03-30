---
title: Komma igång med U-SQL-språk i Azure Data Lake Analytics
description: Lär dig grunderna i U-SQL-språket i Azure Data Lake Analytics. Skriv din första fråga med hjälp av variabler till extra data från filer, omvandla raduppsättningen och aggregerade data.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672823"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Komma igång med U-SQL i Azure Data Lake Analytics
U-SQL är ett språk som kombinerar deklarativa SQL med tvingande C# så att du kan bearbeta data i valfri skala. Genom den skalbara, distribuerade frågefunktionen i U-SQL kan du effektivt analysera data över relationsarkiv, till exempel Azure SQL Database. Med U-SQL kan du bearbeta ostrukturerade data genom att använda schema för att läsa och infoga anpassad logik och UDF:er. Dessutom innehåller U-SQL utökningsbarhet som ger dig finkornig kontroll över hur du kör i stor skala. 

## <a name="learning-resources"></a>Inlärningsresurser

* [U-SQL Tutorial](https://aka.ms/usqltutorial) ger en guidad genomgång av de flesta av U-SQL-språket. Det här dokumentet rekommenderas läsning för alla utvecklare som vill lära sig U-SQL.
* Detaljerad information om **U-SQL-språksyntaxen**finns i [U-SQL Language Reference](https://docs.microsoft.com/u-sql/).
* För att förstå **U-SQL design filosofi,** se Visual Studio blogginlägg [Införa U-SQL - Ett språk som gör Big Data Processing Easy](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Krav

Innan du går igenom U-SQL-exemplen i det här dokumentet läser och slutför [du självstudiekurs: Utveckla U-SQL-skript med Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Den självstudien förklarar mekaniken i att använda U-SQL med Azure Data Lake Tools för Visual Studio.

## <a name="your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande U-SQL-skript är enkelt och låter oss utforska många aspekter U-SQL-språket.

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

Det här skriptet har inga omvandlingssteg. Den läser från källfilen som heter `SearchLog.tsv`, schemalägger den och skriver raduppsättningen tillbaka till en fil som heter SearchLog-first-u-sql.csv.

Lägg märke till frågetecknet bredvid `Duration` datatypen i fältet . Det betyder `Duration` att fältet kan vara null.

### <a name="key-concepts"></a>Viktiga begrepp
* **Raduppsättningsvariabler:** Varje frågeuttryck som skapar en raduppsättning kan tilldelas en variabel. U-SQL följer T-SQL variabeln namngivningsmönster (`@searchlog`, till exempel) i skriptet.
* Nyckelordet **EXTRACT** läser data från en fil och definierar schemat vid läsning. `Extractors.Tsv`är en inbyggd U-SQL-utsug för flikavgränsade filer. Du kan utveckla anpassade extractors.
* **UTDATA** skriver data från en raduppsättning till en fil. `Outputters.Csv()`är en inbyggd U-SQL-utdatater för att skapa en kommaavgränsad värdefil. Du kan utveckla anpassade utdatarar.

### <a name="file-paths"></a>Sökvägar för filer

EXTRAHERA- och OUTPUT-satserna använder filsökvägar. Filsökvägar kan vara absoluta eller relativa:

Följande absoluta filsökväg refererar till en `mystore`fil i ett datasjölager med namnet:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Följande filsökväg `"/"`börjar med . Den refererar till en fil i standardkontot För DataSjölagret:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Använda skalärvariabler

Du kan också använda skalärvariabler för att göra skriptunderhållet enklare. Det tidigare U-SQL-skriptet kan också skrivas som:

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

## <a name="transform-rowsets"></a>Omforma raduppsättningar

Använd **SELECT** för att omforma raduppsättningar:

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

WHERE-satsen använder ett [C# Boolean-uttryck](/dotnet/csharp/language-reference/operators/index). Du kan använda C#-uttrycksspråket för att göra egna uttryck och funktioner. Du kan även utföra mer komplexa filtrering genom att kombinera dem med logiska konjunktioner (ANDs) och disjunctions (ORs).

I följande skript används metoden DateTime.Parse() och en konjunktion.

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
 >Den andra frågan fungerar på resultatet av den första raduppsättningen, vilket skapar en komposit av de två filtren. Du kan också återanvända ett variabelnamn och namnen är omfångade lexikalt.

## <a name="aggregate-rowsets"></a>Aggregerade raduppsättningar
U-SQL ger dig välbekanta ORDER BY, GROUP BY och aggregeringar.

Följande fråga hittar den totala varaktigheten per region och visar sedan de fem översta varaktigheterna i ordning.

U-SQL-raduppsättningar bevarar inte sin ordning för nästa fråga. Om du vill beställa en utdata måste du därför lägga till ORDER BY i OUTPUT-satsen:

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

U-SQL ORDER BY-satsen kräver att du använder FETCH-satsen i ett SELECT-uttryck.

U-SQL HAVING-satsen kan användas för att begränsa utdata till grupper som uppfyller HA-villkoret:

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

Avancerade aggregeringsscenarier finns i U-SQL-referensdokumentationen för [mängd-, analytiska och referensfunktioner](/u-sql/built-in-functions)

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
