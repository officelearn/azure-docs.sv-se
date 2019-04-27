---
title: Utveckla U-SQL med Python, R och C# för Azure Data Lake Analytics i Visual Studio Code
description: Lär dig hur du använder koden bakom med Python, R och C# för att skicka jobbet i Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 6c234ad6756f4e65e172bf0ffc0ae5a1d35d109b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814089"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Utveckla U-SQL med Python, R och C# för Azure Data Lake Analytics i Visual Studio Code
Lär dig hur du använder Visual Studio Code (VSCode) att skriva Python, R- och C# code bakom med U-SQL och skicka jobb till Azure Data Lake-tjänsten. Läs mer om Azure Data Lake-verktyg för VSCode, [använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Innan du skriver bakomliggande kod anpassad kod, måste du öppna en mapp eller en arbetsyta i VSCode.


## <a name="prerequisites-for-python-and-r"></a>Krav för Python- och R
Registrera Python och R-tillägg-sammansättningar för ADL-konto. 
1. Öppna ditt konto i portalen.
   - Välj **Översikt**. 
   - Klicka på **exempel på skript**.
2. Klicka på **mer**.
3. Välj **installera U-SQL-tilläggen**. 
4. Bekräftelsemeddelande visas när U-SQL-tillägg har installerats. 

   ![Konfigurera en miljö för python- och R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Installera VSCode Python- och R-tillägg för bästa upplevelser på tjänsten för Python- och R-språket. 

## <a name="develop-python-file"></a>Utveckla Python-fil
1. Klicka på den **ny fil** i din arbetsyta.
2. Skriv din kod i U-SQL. Här följer ett kodexempel.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Högerklicka på en skriptfil och välj sedan **ADL: Generera Python-koden bakom filen**. 
4. Den **xxx.usql.py** fil skapas i arbetsmappen. Skriv din kod i Python-fil. Här följer ett kodexempel.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Högerklicka i **USQL** -fil som du kan klicka på **kompilera skriptet** eller **skicka jobb** till att köra jobbet.

## <a name="develop-r-file"></a>Utveckla R-fil
1. Klicka på den **ny fil** i din arbetsyta.
2. Skriv din kod i U-SQL-filen. Här följer ett kodexempel.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Högerklicka i **USQL** och väljer sedan **ADL: Skapa R-koden bakom filen**. 
4. Den **xxx.usql.r** fil skapas i arbetsmappen. Skriv din kod i R-filen. Här följer ett kodexempel.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Högerklicka i **USQL** -fil som du kan klicka på **kompilera skriptet** eller **skicka jobb** till att köra jobbet.

## <a name="develop-c-file"></a>Utveckla C#-filen
En bakomliggande kod-fil är en C#-fil som är associerad med ett enda U-SQL-skript. Du kan definiera ett skript som är dedikerad till UDO, UDA, UDT och UDF i filen bakomliggande kod. UDO, UDA, UDT och UDF kan användas direkt i skriptet utan att registrera sammansättningen först. Filen bakomliggande kod placeras i samma mapp som dess peering U-SQL-skriptfilen. Om skriptet heter xxx.usql, heter bakomliggande kod som xxx.usql.cs. Om du manuellt ta bort filen bakomliggande kod inaktiveras funktionen bakomliggande kod för dess associerade U-SQL-skript. Mer information om hur du skriver Kundkod för U-SQL-skript finns i [skriva och använda anpassad kod i U-SQL: Användardefinierade funktioner]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Klicka på den **ny fil** i din arbetsyta.
2. Skriv din kod i U-SQL-filen. Här följer ett kodexempel.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Högerklicka i **USQL** och väljer sedan **ADL: Generera CS koden bakom filen**. 
4. Den **xxx.usql.cs** fil skapas i arbetsmappen. Skriv koden i CS-filen. Här följer ett kodexempel.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Högerklicka i **USQL** -fil som du kan klicka på **kompilera skriptet** eller **skicka jobb** till att köra jobbet.

## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL-lokal körning och lokal felsökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Kom igång med Data Lake Analytics med hjälp av PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Använda Data Lake Tools för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda Data Lake Analytics(U-SQL) catalog](data-lake-analytics-use-u-sql-catalog.md)
