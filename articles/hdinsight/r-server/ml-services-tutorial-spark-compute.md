---
title: 'Självstudier: Använd R i en Spark-beräkningskontexten i Azure HDInsight'
description: Självstudiekurs – komma igång med R och Spark på ML-tjänster.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 244c62467f187417bbb9f0e54173aad5a7d26d0a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450284"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Självstudier: Använd R i en Spark-beräkningskontexten i Azure HDInsight

Den här självstudien innehåller en introduktion steg för steg till att använda R-funktioner i Apache Spark som körs på ett kluster som ML-tjänster i Azure HDInsight.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Hämta exempeldata till lokal lagring
> * Kopiera data till standard storage
> * Ställa in datauppsättningen
> * Skapa datakälla
> * Skapa en beräkningskontext för Spark
> * Anpassa en linjär modell
> * Använda sammansatta XDF-filer
> * Konvertera XDF till CSV

## <a name="prerequisites"></a>Förutsättningar

* En ML-Services-kluster i HDInsight. Se [skapa Apache Hadoop-kluster med Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **ML tjänster** för **Klustertyp**.

## <a name="connect-to-rstudio-server"></a>Anslut till RStudio Server

RStudio Server körs på klustrets kantnod. Gå till följande URL: en där `CLUSTERNAME` är namnet på klustret för ML-tjänster som du skapade:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Första gången du loggar in måste du autentisera två gånger. För den första autentiseringsprompten anger du klustret det användarnamn och lösenord, standardvärdet är `admin`. Den andra autentiseringsprompten anger du SSH-inloggning och lösenord, standardvärdet är `sshuser`. Efterföljande inloggningar kräver endast SSH-autentiseringsuppgifter.

## <a name="download-sample-data"></a>Hämta exempeldata

Den *flygbolag 2012 i tid datauppsättning* består av 12 CSV-filer som innehåller information om ankomst och avgång flyginformation för alla kommersiella flygningar inom USA, för år 2012. Det här är en stor mängd data med över sex miljoner observationer.

1. Initiera några miljövariabler. Ange följande kod i RStudio Server-konsolen:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

    Variablerna visas på höger sida av skärmen under den **miljö** fliken.

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

2.  Skapa en lokal katalog och ladda ned exempeldata. Ange följande kod i RStudio:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    Nedladdningen ska slutföras inom cirka 9 och en halv minut.

## <a name="copy-data-to-default-storage"></a>Kopiera data till standard storage

HDFS-plats har angetts med den `airDataDir` variabeln. Ange följande kod i RStudio:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Steget bör slutföras inom 10 sekunder.

## <a name="set-up-data-set"></a>Ställa in datauppsättningen

1. Skapa ett objekt i filsystemet som använder standardvärden. Ange följande kod i RStudio:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

2. De ursprungliga CSV-filerna har i stället svårhanterlig variabelnamn, så vi anger en `colInfo` listan om du vill göra dem mer hanterbara. Ange följande kod i RStudio:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-source"></a>Skapa datakälla

I en Spark-beräkningskontext kan du skapa datakällor med hjälp av följande funktioner:

|Funktion | Beskrivning |
|---------|-------------|
|`RxTextData` | En kommaavgränsad textsträng datakälla. |
|`RxXdfData` | Data i data för XDF-format. I RevoScaleR ändra formatet för XDF för Hadoop för att lagra data i en sammansatt uppsättning filer i stället för en enskild fil. |
|`RxHiveData` | Genererar ett källobjekt Hive-Data.|
|`RxParquetData` | Genererar en datakälla för Parquet-objektet.|
|`RxOrcData` | Genererar en datakälla för Orc-objektet.|

Skapa en [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) objekt med hjälp av de filer som du kopierade till HDFS. Ange följande kod i RStudio:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-compute-context-for-spark"></a>Skapa en beräkningskontext för Spark

För att läsa in data och köra analyser på arbetsnoder, du ställa in beräkningskontexten i skriptet till [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). I det här sammanhanget distribuerar R-funktioner som automatiskt arbetsbelastningen över alla arbetarnoder utan inbyggda krav för att hantera jobb eller kön. Spark-beräkningskontexten upprättas via `RxSpark` eller `rxSparkConnect()` att skapa Spark-beräkningskontexten och använder `rxSparkDisconnect()` att återgå till en lokal beräkningskontext. Ange följande kod i RStudio:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Anpassa en linjär modell

1. Använd den [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) för att anpassa en linjär modell med hjälp av din `airDS` datakälla. Ange följande kod i RStudio:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Det här steget bör slutföras mellan 2 och 3 minuter.

1. Granska resultaten. Ange följande kod i RStudio:

    ```R
    summary(delayArr)
    ```

    Du bör se följande resultat:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Observera att resultatet anger vi har bearbetat alla data, sex miljoner observationer, med alla CSV-filer i den angivna katalogen. Observera också att eftersom vi angav `cube = TRUE`, vi har en beräknad koefficienten för varje dag i veckan (och inte skärningspunkt).

## <a name="use-composite-xdf-files"></a>Använda sammansatta XDF-filer

Som vi har sett du kan analysera CSV-filer direkt med R på Hadoop, men analysen kan göras snabbare om data lagras i ett format som är mer effektiv. R .xdf-formatet är mycket effektivt, men har ändrats något för HDFS så att enskilda filer finnas kvar i ett enda HDFS-block. (Blockstorleken HDFS varierar från installationen till installationen men är vanligtvis 64 MB eller 128 MB). När du använder [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) på Hadoop kan du ange en `RxTextData` datakälla som `AirDS` som inData och en `RxXdfData` datakälla med filsystemet är inställda som ett HDFS-filsystemets som argumentet Utfil att skapa en uppsättning sammansatta .xdf filer. Den `RxXdfData` objekt kan sedan användas som argument för data i efterföljande R-analyser.

1. Definiera en `RxXdfData` objekt. Ange följande kod i RStudio:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Ange en blockstorlek 250000 rader och ange att vi läser alla data. Ange följande kod i RStudio:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importera data med `rxImport`. Ange följande kod i RStudio:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Det här steget ska slutföras inom några minuter.

1. Beräkna samma linjär modell med nya, snabba datakällan igen. Ange följande kod i RStudio:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Steget bör vara klart på mindre än en minut.

1. Granska resultaten. Resultatet bör vara samma från och med den CSV-filer. Ange följande kod i RStudio:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Konvertera XDF till CSV

### <a name="in-a-spark-context"></a>I en Spark-kontext

Om du har konverterat din CSV till XDF att dra nytta av effektiviteten samtidigt som du kör analyser, men nu vill omvandla dina data tillbaka till CSV kan du göra det med hjälp av [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Om du vill skapa en mapp med CSV-filer, först skapa en `RxTextData` objekt med namnet på en katalog som filen argument; representerar den mapp där du vill skapa CSV-filer. Den här katalogen skapas när du kör den `rxDataStep`. Peka sedan på det här `RxTextData` objekt i den `outFile` argumentet för den `rxDataStep`. Varje CSV skapade namnges baserat på katalognamnet och följt av ett tal.

Anta att vi vill skriva ut en mapp med klusterdelade volymer i HDFS från vår `airDataXdf` sammansatta XDF när vi utförde logistic regression- och förutsägelsetransaktioner, så att de nya CSV-filerna som innehåller de förväntade värdena och restbelopp. Ange följande kod i RStudio:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Det här steget bör slutföras inom två och en halv minuter.

Du Observera att den `rxDataStep` platskontrollen skrevs ut en CSV-fil för varje .xdfd-fil i sammansatt XDF filen. Detta är standardbeteendet för att skriva CSV från sammansatta XDF till HDFS när beräkningskontexten är inställd på `RxSpark`.

### <a name="in-a-local-context"></a>I en lokal kontext

Du kan också växla din beräkningskontext tillbaka till `local` när du är klar utför dina analyser och dra nytta av två argument inom `RxTextData` som ger dig lite mer kontroll när du skriver ut CSV-filer till HDFS: `createFileSet` och `rowsPerOutFile`. När `createFileSet` är inställd på `TRUE`, en mapp med CSV-filer skrivs till den katalog som du anger. När `createFileSet` är inställd på `FALSE` en CSV-fil skrivs. Det andra argumentet `rowsPerOutFile`, kan anges till ett heltal som anger hur många rader som att skriva till varje CSV-fil när `createFileSet` är `TRUE`.

Ange följande kod i RStudio:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Det här steget ska slutföras inom cirka 10 minuter.

När du använder en `RxSpark` beräkningskontext, `createFileSet` som standard `TRUE` och `rowsPerOutFile` har ingen effekt. Så om du vill skapa en enda CSV-fil eller anpassa antalet rader per fil som du måste utföra den `rxDataStep` i en `local` beräkningskontext (data kan fortfarande vara i HDFS).

## <a name="final-steps"></a>Sista stegen

1. Rensa Data. Ange följande kod i RStudio:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Stoppa remote Spark-programmet. Ange följande kod i RStudio:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Avsluta R-session. Ange följande kod i RStudio:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster, se [ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda R-funktioner i Apache Spark som körs på ett kluster som ML-tjänster i Azure HDInsight. Mer information finns i följande artiklar:

* [Alternativ för beräkningskontexter för ML-tjänster på HDInsight](r-server-compute-contexts.md)
* [R-funktioner för Spark på Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
