---
title: Utöka U-SQL-skript med R i Azure Data Lake Analytics
description: Lär dig hur du kör R-kod i U-SQL-skript med Hjälp av Azure Data Lake Analytics. Bädda in R-kod infogad eller referens från filer.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672687"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Utöka U-SQL-skript med R-kod i Azure Data Lake Analytics

I följande exempel visas de grundläggande stegen för distribution av R-kod:
* Använd `REFERENCE ASSEMBLY` uttrycket för att aktivera R-tillägg för U-SQL Script.
* Använd `REDUCE` åtgärden för att partitionera indata på en nyckel.
* R-tilläggen för U-SQL innehåller en inbyggd`Extension.R.Reducer`reducer () som kör R-kod på varje hörn som tilldelats reducer. 
* Användning av dedikerade namngivna dataramar som anropas `inputFromUSQL` respektive `outputToUSQL` för att överföra data mellan U-SQL- och R. DataFrame-identifieraren för indata och utdata är fasta (det vill säga användare kan inte ändra dessa fördefinierade namn på indata- och utdatadataramidentifierare).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Bädda in R-kod i U-SQL-skriptet

Du kan infoga R-koden ditt U-SQL-skript med `Extension.R.Reducer`hjälp av kommandoparametern i . Du kan till exempel deklarera R-skriptet som en strängvariabel och skicka det som en parameter till Reducer.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Behåll R-koden i en separat fil och referera till U-SQL-skriptet

Följande exempel illustrerar en mer komplex användning. I det här fallet distribueras R-koden som en RESURS som är U-SQL-skriptet.

Spara den här R-koden som en separat fil.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Använd ett U-SQL-skript för att distribuera R-skriptet med DEPLOY RESOURCE-uttrycket.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Hur R integreras med U-SQL

### <a name="datatypes"></a>Datatyper
* Sträng- och numeriska kolumner från U-SQL konverteras som-är mellan R `double`DataFrame och U-SQL [stöds typer: `string`, `bool`, `integer`, `byte`].
* Datatypen `Factor` stöds inte i U-SQL.
* `byte[]`serialiseras som en base64-kodad `string`.
* U-SQL strängar kan konverteras till faktorer i R-kod, när U-SQL skapa R `stringsAsFactors: true`indataram eller genom att ställa in parametern reducer .

### <a name="schemas"></a>Scheman
* U-SQL-datauppsättningar kan inte ha dubblettkolumnnamn.
* Kolumnnamn för U-SQL-datauppsättningar måste vara strängar.
* Kolumnnamnen måste vara desamma i U-SQL- och R-skript.
* Readonly-kolumnen kan inte ingå i dataramen för utdata. Eftersom readonly-kolumner injiceras automatiskt tillbaka i U-SQL-tabellen om det är en del av utdataschemat för UDO.

### <a name="functional-limitations"></a>Funktionella begränsningar
* R-motorn kan inte instansieras två gånger i samma process. 
* För närvarande stöder U-SQL inte Kombinerare UDOs för förutsägelse med partitionerade modeller som genereras med hjälp av Reducer UDOs. Användare kan deklarera de partitionerade modellerna som resurs och använda `ExtR_PredictUsingLMRawStringReducer.usql`dem i sitt R Script (se exempelkod)

### <a name="r-versions"></a>R-versioner
Endast R 3.2.2 stöds.

### <a name="standard-r-modules"></a>Standard R-moduler

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Begränsningar för in- och utdatastorlek
Varje hörn har en begränsad mängd minne som tilldelats den. Eftersom dataramarna för in- och utdata måste finnas i minnet i R-koden får den totala storleken för in- och utdata inte överstiga 500 MB.

### <a name="sample-code"></a>Exempelkod
Mer exempelkod finns i ditt DataSjö store-konto när du har installerat tilläggen U-SQL Advanced Analytics. Sökvägen för mer exempelkod är: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Distribuera anpassade R-moduler med U-SQL

Först skapa en R anpassad modul och zip det och sedan ladda upp zippade R anpassade modulfilen till din ADL butik. I exemplet kommer vi att ladda upp magittr_1.5.zip till roten av adls-standardkontot för adla-kontot som vi använder. När du har laddat upp modulen till ADL-arkivet deklarerar du den `install.packages` som använd DEPLOY RESOURCE för att göra den tillgänglig i ditt U-SQL-skript och anropar för att installera den.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Efterföljande moment
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda U-SQL-fönsterfunktioner för Azure Data Lake Analytics-jobb](data-lake-analytics-use-window-functions.md)
