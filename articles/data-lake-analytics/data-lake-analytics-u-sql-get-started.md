---
title: Kom igång med U-SQL-språket i Azure Data Lake Analytics
description: Lär dig grunderna i U-SQL-språket i Azure Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 9de5c7228944bd0448d9dfa833ef223140ccf0e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59488719"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Kom igång med U-SQL i Azure Data Lake Analytics
U-SQL är ett språk som kombinerar deklarativt Frågespråk med tvingande C# för så att du kan bearbeta data i valfri skala. Via funktionen skalbar och distribuerad fråga för U-SQL, kan du effektivt analysera data över relationella datalager som Azure SQL Database. Med U-SQL, kan du bearbeta Ostrukturerade data med hjälp av schema vid läsning och lägga till anpassad logik och UDF: er. Dessutom innehåller U-SQL utökningsbarhet som ger dig detaljerad kontroll över hur du kör i skala. 

## <a name="learning-resources"></a>Utbildningsresurser

* Den [U-SQL-självstudien](https://aka.ms/usqltutorial) innehåller stegvisa anvisningar för de flesta av U-SQL-språket. Det här dokumentet är rekommenderad läsning för alla utvecklare som vill lära dig U-SQL.
* Detaljerad information om den **syntaxen för U-SQL**, finns i den [U-SQL-Språkreferens](https://go.microsoft.com/fwlink/p/?LinkId=691348).
* Att förstå den **U-SQL-designfilosofin**, finns i Visual Studio blogginlägget [introducerar U-SQL – ett språk som gör Big databehandling enkelt](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du går igenom U-SQL-exemplen i det här dokumentet kan läsa och slutföra [självstudien: Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Självstudien förklaras säkerhetsnivån med U-SQL med Azure Data Lake Tools för Visual Studio.

## <a name="your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande U-SQL-skript är enkelt och låt oss se många aspekter av U-SQL-språket.

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

Det här skriptet har inte någon transformeringssteg. Det läser från källfilen kallas `SearchLog.tsv`schematizes den och skriver raduppsättningen tillbaka till en fil med namnet SearchLog-först-u-sql.csv.

Lägg märke till frågetecken bredvid data skriver i den `Duration` fält. Det innebär att den `Duration` fält kan vara null.

### <a name="key-concepts"></a>Viktiga begrepp
* **Raduppsättningen variabler**: Varje frågeuttryck som producerar en raduppsättning kan tilldelas till en variabel. U-SQL följer T-SQL variabeln namngivningsmönstret (`@searchlog`, till exempel) i skriptet.
* Den **EXTRAHERA** nyckelordet läser data från en fil och definierar schemat vid läsning. `Extractors.Tsv` är en inbyggd U-SQL-extraktor för filer fliken med kommaavgränsade värden. Du kan utveckla anpassade extraktorer.
* Den **utdata** skriver data från en raduppsättning till en fil. `Outputters.Csv()` är en inbyggd U-SQL-outputter för att skapa en fil med kommatecken avgränsade värden. Du kan utveckla anpassade utdatafunktioner.

### <a name="file-paths"></a>Sökvägar

EXTRAHERA och utdata-uttryck använda sökvägar. Sökvägar kan vara absolut eller relativ:

Den här följande absoluta sökvägen refererar till en fil i ett Data Lake Store med namnet `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Den här följande sökväg som börjar med `"/"`. Den refererar till en fil i Data Lake Store-kontot av standardtyp:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Använd skalbara variabler

Du kan använda skalbara variabler samt för att underlätta skript-underhåll. Föregående U-SQL-skript kan också anges som:

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

## <a name="transform-rowsets"></a>Omvandla raduppsättningar

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

WHERE-satsen använder en [C# booleskt uttryck](/dotnet/csharp/language-reference/operators/index). Du kan använda C#-Uttrycksspråk för att göra egna uttryck och funktioner. Du kan även utföra mer komplex filtrering genom att kombinera dem med logiska konjunktioner (ANDs) och disjunctions (ORs).

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
 >Den andra frågan körs på resultatet av den första raduppsättningen, vilket skapar en kombination av två filter. Du kan även återanvända ett variabelnamn och namnen är begränsade lexically.

## <a name="aggregate-rowsets"></a>Sammanställd raduppsättningar
U-SQL ger dig den välkända ORDER BY, GROUP BY och aggregeringar.

Följande fråga söker efter den totala varaktigheten per region, och visar upp fem varaktigheter i ordning.

U-SQL-raduppsättningar bevaras inte deras inbördes ordning för nästa fråga. Därför om du vill beställa utdata, måste du lägga till ORDER BY i utdata-instruktionen:

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

U-SQL ORDER BY-satsen kräver FETCH-sats i en SELECT-uttryck.

MED U-SQL-satsen kan användas för att begränsa utdata till grupper som uppfyller HAVING-villkor:

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

Avancerade aggregering scenarier, finns i dokumentationen för U-SQL-referens för [aggregeras, analytiska och referera till funktioner](/u-sql/built-in-functions)

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
