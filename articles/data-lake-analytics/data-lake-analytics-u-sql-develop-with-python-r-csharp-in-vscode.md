---
title: Köra U-SQL-jobb i Python, R och C# - Azure Data Lake Analytics
description: Lär dig hur du använder koden bakom med Python, R och C# för att skicka jobb i Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: cb3ddf0c4147fa982e8ab0f9d440292d12803d35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309712"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Utveckla U-SQL med Python, R och C# för Azure Data Lake Analytics i Visual Studio-kod
Lär dig hur du använder Visual Studio Code (VSCode) för att skriva Python-, R- och C#-kod bakom med U-SQL och skicka jobb till Azure Data Lake-tjänsten. Mer information om Azure Data Lake Tools för VSCode finns [i Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Innan du skriver anpassad kod för kod måste du öppna en mapp eller en arbetsyta i VSCode.


## <a name="prerequisites-for-python-and-r"></a>Förutsättningar för Python och R
Registrera Python och R-tilläggssammansättningar för ditt ADL-konto. 
1. Öppna ditt konto i portalen.
   - Välj **Översikt**. 
   - Klicka på **Exempelskript**.
2. Klicka på **Mer**.
3. Välj **Installera U-SQL-tillägg**. 
4. Bekräftelsemeddelande visas när U-SQL-tilläggen har installerats. 

   ![Konfigurera miljön för python och R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > För bästa möjliga upplevelser på Python och R språktjänst, installera VSCode Python och R förlängning. 

## <a name="develop-python-file"></a>Utveckla Python-fil
1. Klicka på den **nya filen** på arbetsytan.
2. Skriv koden i U-SQL. Följande är ett kodexempel.
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
    
3. Högerklicka på en skriptfil och välj sedan **ADL: Generera Python-kod bakom fil**. 
4. Den **xxx.usql.py** filen genereras i arbetsmappen. Skriv din kod i Python-filen. Följande är ett kodexempel.

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
5. Högerklicka i **USQL-filen,** kan du klicka på **Kompilera skript** eller **Skicka jobb** till löpande jobb.

## <a name="develop-r-file"></a>Utveckla R-fil
1. Klicka på den **nya filen** på arbetsytan.
2. Skriv koden i U-SQL-filen. Följande är ett kodexempel.
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
3. Högerklicka i **USQL-filen** och välj sedan **ADL: Generera R-kod bakom fil**. 
4. Filen **xxx.usql.r** genereras i arbetsmappen. Skriv koden i R-filen. Följande är ett kodexempel.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Högerklicka i **USQL-filen,** kan du klicka på **Kompilera skript** eller **Skicka jobb** till löpande jobb.

## <a name="develop-c-file"></a>Utveckla C#-fil
En bakom-fil är en C#-fil som är associerad med ett enda U-SQL-skript. Du kan definiera ett skript som är dedikerat till UDO, UDA, UDT och UDF i den bakomkodade filen. UDO, UDA, UDT och UDF kan användas direkt i skriptet utan att registrera sammansättningen först. Den bakomliggande koden läggs i samma mapp som dess peering U-SQL-skriptfil. Om skriptet heter xxx.usql, namnges den bakomkoda som xxx.usql.cs. Om du tar bort den bakom koden manuellt inaktiveras bakom-funktionen för kod för det associerade U-SQL-skriptet. Mer information om hur du skriver kundkod för U-SQL-skript finns i [Skriva och använda anpassad kod i U-SQL: Användardefinierade funktioner]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Klicka på den **nya filen** på arbetsytan.
2. Skriv koden i U-SQL-filen. Följande är ett kodexempel.
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
3. Högerklicka i **USQL-filen** och välj sedan **ADL: Generera CS-kod bakom fil**. 
4. Den **xxx.usql.cs** filen genereras i arbetsmappen. Skriv koden i CS-filen. Följande är ett kodexempel.

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
5. Högerklicka i **USQL-filen,** kan du klicka på **Kompilera skript** eller **Skicka jobb** till löpande jobb.

## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL lokal körning och lokal felsökning med Visual Studio-kod](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Komma igång med DataSjöanalys med PowerShell](data-lake-analytics-get-started-powershell.md)
* [Komma igång med Data Lake Analytics med Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Använda Data Lake Tools för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda katalogen DataSjöanalys(U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
