---
title: Utöka med R i Azure Data Lake Analytics U-SQL-skript
description: Lär dig hur du kör R-kod i U-SQL-skript med hjälp av Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 59a52b2aeb83732a608f1fcf5bc4de907d25dfd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885034"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Utöka med R-kod i Azure Data Lake Analytics U-SQL-skript

I följande exempel visas de grundläggande stegen för att distribuera R-kod:
* Använd den `REFERENCE ASSEMBLY` -uttrycket för att aktivera R-tillägg för U-SQL-skriptet.
* Använd den `REDUCE` åtgärden att partitionera inkommande data på en nyckel.
* R-tillägg för U-SQL är en inbyggd reducer (`Extension.R.Reducer`) som kör R-kod på varje brytpunkt som tilldelats reducer. 
* Användning av dedikerade med namnet dataramar kallas `inputFromUSQL` och `outputToUSQL` att överföra data mellan U-SQL och R. indata och utdata DataFrame identifierarnamn korrigeras (dvs, användare kan inte ändra dessa fördefinierade namnen på indata och utdata DataFrame identifierare).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Bädda in R-kod i U-SQL-skript

Du kan infogade R code U-SQL-skript med hjälp av Kommandoparametern för den `Extension.R.Reducer`. Du kan exempelvis deklarera R-skriptet som en strängvariabel och skickar den som en parameter till Reducer.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Håll R-koden i en separat fil och referera till det U-SQL-skript

I följande exempel visas en mer avancerad användning. I det här fallet distribueras R-kod som en resurs som U-SQL-skriptet.

Spara den här R-kod som en separat fil.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Använd ett U-SQL-skript för att distribuera den R-skripten med DISTRIBUERA RESOURCE-instruktionen.

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

## <a name="how-r-integrates-with-u-sql"></a>Hur R kan integreras med U-SQL

### <a name="datatypes"></a>Datatyper
* Konverteras som strängvärden och numeriska kolumner från U-SQL-är mellan R DataFrame och U-SQL [typer som stöds: `double`, `string`, `bool`, `integer`, `byte`].
* Den `Factor` stöds inte i U-SQL.
* `byte[]` måste serialiseras som en base64-kodad `string`.
* U-SQL-strängar kan konverteras till faktorer i R-kod när U-SQL skapa inkommande dataram i R eller genom att ange parametern reducer `stringsAsFactors: true`.

### <a name="schemas"></a>Scheman
* U-SQL-datauppsättningar kan inte ha duplicerade kolumnnamn.
* U-SQL datauppsättningar kolumnnamnen måste vara strängar.
* Kolumnnamnen måste vara samma i U-SQL, R-skript.
* ReadOnly-kolumn kan inte ingå i utdata-dataframe. Eftersom readonly kolumner matas ut automatiskt tillbaka i U-SQL-tabell om det är en del av utdata-schemat för UDO.

### <a name="functional-limitations"></a>Funktionella begränsningar
* R-motorn kan inte initieras två gånger i samma process. 
* U-SQL stöder för närvarande inte Combiner UDO för förutsägelse med partitionerade modeller som skapats med Reducer UDO. Användare kan deklarera partitionerade modeller som resurs och använda dem i deras R-skript (se kodproverna `ExtR_PredictUsingLMRawStringReducer.usql`)

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

### <a name="input-and-output-size-limitations"></a>Indata och utdata begränsningar
Varje hörn har en begränsad mängd minne som tilldelats den. Eftersom inkommande och utgående dataramar måste finnas i minnet i R-kod, får inte den totala storleken för indata och utdata överskrida 500 MB.

### <a name="sample-code"></a>Exempelkod
Mer exempelkod är tillgänglig i ditt Data Lake Store-konto när du har installerat U-SQL Advanced Analytics extensions. Sökvägen för mer exempelkoden är: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Distribuera anpassade R-moduler med U-SQL

Först skapar du en anpassad R-modul och zip den och sedan ladda upp den komprimerade anpassade R-modulfilen till ADL-store. I det här exemplet kommer vi att överföra magittr_1.5.zip till roten i standard ADLS-konto för ADLA-kontot som vi använder. När du överför modulen till ADL store deklarera den använda DISTRIBUERA resurs för att göra den tillgänglig i dina U-SQL-skript och anropa `install.packages` att installera den.

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

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Med hjälp av U-SQL-fönstrets funktioner för Azure Data Lake Analytics-jobb](data-lake-analytics-use-window-functions.md)
