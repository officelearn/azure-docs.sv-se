---
title: "Utveckla U-SQL med Python, R och C# för Azure Data Lake Analytics i Visual Studio Code | Microsoft Docs"
description: "Lär dig mer om att använda koden bakom med Python, R och C# för att skicka jobbet i Azure Data Lake."
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: 
editor: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/22/2017
ms.author: jejiang
ms.openlocfilehash: 8fd8decfde1220be6aaa099c3afb24e8c1eecce4
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Utveckla U-SQL med Python, R och C# för Azure Data Lake Analytics i Visual Studio Code
Lär dig hur du använder Visual Studio Code (VSCode) att skriva Python, R och C# code bakom med U-SQL och skicka jobb till Azure Data Lake-tjänsten. Mer information om Azure Data Lake-verktyg för VSCode finns [använda Azure Data Lake-verktyg för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Innan du skriver bakomliggande kod anpassad kod, måste du öppna en mapp eller en arbetsyta i VSCode.


## <a name="prerequisites-for-python-and-r"></a>Krav för Python och R
Registrera Python och R-tillägg-sammansättningar för ADL-konto. 
1. Öppna ditt konto i portalen.
   - Välj **översikt**. 
   - Klicka på **exempel på skript**.
2. Klicka på **mer**.
3. Välj **installera tillägg för U-SQL**. 
4. Bekräftelsemeddelandet visas när U-SQL-tillägg har installerats. 

  ![Ställa in miljön för python och R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

  > [!Note]
  > Bästa upplevelser i Python och R språk-tjänsten, installera VSCode Python och R-tillägget. 

## <a name="develop-python-file"></a>Utveckla Python-fil
1. Klicka på den **ny fil** på arbetsytan.
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
    
3. Högerklicka på en skriptfil och välj sedan **ADL: Generera Python koden bakom filen**. 
4. Den **xxx.usql.py** -filen har genererats i arbetsmappen. Skriv koden i Python-fil. Följande är ett kodexempel.

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
5. Högerklicka i **USQL** filen som du kan klicka på **kompilera skriptet** eller **skicka jobbet** att köra jobb.

## <a name="develop-r-file"></a>Utveckla R-fil
1. Klicka på den **ny fil** på arbetsytan.
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
3. Högerklicka i **USQL** filen och välj sedan **ADL: Generera R-koden bakom filen**. 
4. Den **xxx.usql.r** -filen har genererats i arbetsmappen. Skriv koden i R-filen. Följande är ett kodexempel.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Högerklicka i **USQL** filen som du kan klicka på **kompilera skriptet** eller **skicka jobbet** att köra jobb.

## <a name="develop-c-file"></a>Utveckla C#-filen
En fil med bakomliggande kod är ett C#-fil som är associerad med ett enda U-SQL-skript. Du kan definiera ett skript som är dedikerad till UDO, UDA, UDT och UDF i filen bakomliggande kod. UDO, UDA, UDT och UDF kan användas direkt i skriptet utan att registrera sammansättningen först. Bakomliggande kod filen placeras i samma mapp som dess peering U-SQL-skriptfilen. Om skriptet heter xxx.usql, heter bakomliggande kod som xxx.usql.cs. Om du manuellt ta bort filen bakomliggande kod inaktiveras funktionen bakomliggande kod för dess associerade U-SQL-skript. Mer information om hur du skriver kunden koden för U-SQL-skript finns [skrivning och använder anpassad kod i U-SQL: användardefinierade funktioner]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Klicka på den **ny fil** på arbetsytan.
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
3. Högerklicka i **USQL** filen och välj sedan **ADL: Generera CS koden bakom filen**. 
4. Den **xxx.usql.cs** -filen har genererats i arbetsmappen. Skriv koden i CS-filen. Följande är ett kodexempel.

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
5. Högerklicka i **USQL** filen som du kan klicka på **kompilera skriptet** eller **skicka jobbet** att köra jobb.

## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Lokal U-SQL kör och lokala debug med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Utveckla U-SQL-sammansättningar för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-assemblies.md)
* [Kom igång med Data Lake Analytics med hjälp av PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Använd Data Lake-verktyg för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använd Data Lake Analytics(U-SQL) katalog](data-lake-analytics-use-u-sql-catalog.md)
