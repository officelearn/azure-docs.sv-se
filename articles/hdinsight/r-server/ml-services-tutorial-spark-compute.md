---
title: 'Självstudiekurs: Använd R i en Spark-beräkningskontext i Azure HDInsight'
description: Självstudiekurs – Kom igång med R och Spark i ett Azure HDInsight Machine Learning-tjänstkluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121928"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Självstudiekurs: Använd R i en Spark-beräkningskontext i Azure HDInsight

Den här självstudien ger en steg-för-steg-introduktion till att använda R-funktionerna i Apache Spark som körs på ett Azure HDInsight Machine Learning-tjänstkluster.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Ladda ned exempeldata till lokal lagring
> * Kopiera data till standardlagring
> * Konfigurera en datauppsättning
> * Skapa datakällor
> * Skapa en beräkningskontext för Spark
> * Montera en linjär modell
> * Använda sammansatta XDF-filer
> * Konvertera XDF till CSV

## <a name="prerequisites"></a>Krav

* Ett azure hdinsight machine learning-tjänstkluster. Gå till [Skapa Apache Hadoop-kluster med hjälp av Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **ML-tjänster**för **klustertyp**.

## <a name="connect-to-rstudio-server"></a>Anslut till RStudio Server

RStudio Server körs på klustrets kantnod. Gå till följande plats (där *CLUSTERNAME* i URL:en är namnet på det HDInsight Machine Learning-tjänstklustret som du skapade):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Första gången du loggar in autentiserar du två gånger. Vid den första autentiseringsprompten anger du användarnamn och lösenord för klusteradministratör (standard är *admin*). Vid den andra autentiseringsprompten anger du SSH-användarnamnet och lösenordet (standardvärdet är *sshuser).* Efterföljande inloggningar kräver endast SSH-autentiseringsuppgifterna.

## <a name="download-the-sample-data-to-local-storage"></a>Ladda ned exempeldata till lokal lagring

*Airline 2012 On-Time Data Set* består av 12 kommaavgränsade filer som innehåller flyg ankomst och avgång detaljer för alla kommersiella flygningar inom USA för år 2012. Denna datauppsättning är stor, med över 6 miljoner observationer.

1. Initiera några miljövariabler. Ange följande kod i RStudio Server-konsolen:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. Välj fliken **Miljö** i den högra rutan. Variablerna visas under **Värden**.

    ![HdInsight R studio webbkonsol](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Skapa en lokal katalog och hämta exempeldata. I RStudio anger du följande kod:

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

    Nedladdningen bör vara klar i ca 9,5 minuter.

## <a name="copy-the-data-to-default-storage"></a>Kopiera data till standardlagring

Platsen för Hadoop Distributed File System (HDFS) anges med variabeln. `airDataDir` I RStudio anger du följande kod:

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

Steget ska vara klart om cirka 10 sekunder.

## <a name="set-up-a-dataset"></a>Konfigurera en datauppsättning

1. Skapa ett filsystemobjekt som använder standardvärdena. I RStudio anger du följande kod:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Eftersom de ursprungliga CSV-filerna har ganska otympliga variabelnamn, anger du en *colInfo-lista* för att göra dem mer hanterbara. I RStudio anger du följande kod:

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

## <a name="create-data-sources"></a>Skapa datakällor

I en Spark-beräkningskontext kan du skapa datakällor med hjälp av följande funktioner:

|Funktion | Beskrivning |
|---------|-------------|
|`RxTextData` | En kommaavgränsad textdatakälla. |
|`RxXdfData` | Data i XDF-datafilformat. I RevoScaleR ändras XDF-filformatet för Att Hadoop ska kunna lagra data i en sammansatt uppsättning filer i stället för en enda fil. |
|`RxHiveData` | Genererar ett Hive-datakällobjekt.|
|`RxParquetData` | Genererar ett parettdatakällaobjekt.|
|`RxOrcData` | Genererar ett Orc-datakällobjekt.|

Skapa ett [RxTextData-objekt](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) med hjälp av filerna som du kopierade till HDFS. I RStudio anger du följande kod:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Skapa en beräkningskontext för Spark

Om du vill läsa in data och köra analyser på arbetsnoder anger du beräkningskontexten i skriptet till [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). I det här sammanhanget distribuerar R-funktioner automatiskt arbetsbelastningen över alla arbetsnoder, utan några inbyggda krav för att hantera jobb eller kön. Spark-beräkningskontexten `RxSpark` `rxSparkConnect()` upprättas genom eller för att `rxSparkDisconnect()` skapa Spark-beräkningskontexten och används för att återgå till en lokal beräkningskontext. I RStudio anger du följande kod:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Montera en linjär modell

1. Använd [funktionen rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) för att passa `airDS` en linjär modell med hjälp av datakällan. I RStudio anger du följande kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Detta steg bör vara klar i 2 till 3 minuter.

1. Granska resultaten. I RStudio anger du följande kod:

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

    Resultaten visar att du har bearbetat alla data, 6 miljoner observationer, med hjälp av alla CSV-filer i den angivna katalogen. Eftersom du `cube = TRUE`har angett har du en uppskattad koefficient för varje dag i veckan (och inte skärningspunkten).

## <a name="use-composite-xdf-files"></a>Använda sammansatta XDF-filer

Som du har sett kan du analysera CSV-filer direkt med R på Hadoop. Men du kan göra analysen snabbare om du lagrar data i ett effektivare format. R XDF-filformatet är effektivt, men det har ändrats något för HDFS så att enskilda filer förblir inom ett enda HDFS-block. (HDFS-blockstorleken varierar från installation till installation, men är vanligtvis antingen 64 MB eller 128 MB.) 

När du använder [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) på Hadoop för att skapa en `RxTextData` uppsättning sammansatta XDF-filer anger du en datakälla `AirDS` som inData och en `RxXdfData` datakälla med fileSystem inställt på ett HDFS-filsystem som argumentet outFile. Du kan sedan `RxXdfData` använda objektet som dataargument i efterföljande R-analyser.

1. Definiera `RxXdfData` ett objekt. I RStudio anger du följande kod:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Ange en blockstorlek på 250000 rader och ange att vi läser alla data. I RStudio anger du följande kod:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importera data `rxImport`med . I RStudio anger du följande kod:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Det här steget bör vara klart om några minuter.

1. Re-estimate samma linjära modell, med hjälp av den nya, snabbare datakällan. I RStudio anger du följande kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Steget ska vara klart på mindre än en minut.

1. Granska resultaten. Resultaten bör vara desamma som från CSV-filerna. I RStudio anger du följande kod:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Konvertera XDF till CSV

### <a name="in-a-spark-context"></a>I en Spark-kontext

Om du konverterade CSV-filerna till XDF-filformat för större effektivitet när du kör analyserna, men nu vill konvertera dina data tillbaka till CSV, kan du göra det med hjälp av [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Om du vill skapa en mapp `RxTextData` med CSV-filer skapar du först ett objekt med ett katalognamn som filargument. Det här objektet representerar den mapp där CSV-filerna ska skapas. Den här katalogen skapas när du kör `rxDataStep`. Peka sedan på `RxTextData` det `outFile` här objektet `rxDataStep`i argumentet för . Varje CSV som skapas namnges baserat på katalognamnet och följs av ett tal.

Anta att du vill skriva ut en mapp med `airDataXdf` CSV-filer i HDFS från din sammansatta XDF när du har utfört den logistiska regressionen och förutsägelsen, så att de nya CSV-filerna innehåller de förväntade värdena och resterna. I RStudio anger du följande kod:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Detta steg bör vara klar i ca 2,5 minuter.

Den `rxDataStep` skrev ut en CSV-fil för varje XDFD-fil i indatakomposit XDF-filen. Detta är standardbeteendet för att skriva CSV-filer från sammansatta XDF-filer till HDFS när beräkningskontexten är inställd på `RxSpark`.

### <a name="in-a-local-context"></a>I ett lokalt sammanhang

Alternativt, när du är klar med dina analyser, kan du `local` växla din beräkningskontext tillbaka till för att dra nytta av två argument inom `RxTextData` som ger dig lite mer kontroll när du skriver ut CSV-filer till HDFS: `createFileSet` och `rowsPerOutFile`. När du `createFileSet` `TRUE`ställer in på skrivs en mapp med CSV-filer till den katalog som du anger. När du `createFileSet` `FALSE`ställer in på skrivs en enda CSV-fil. Du kan ange det `rowsPerOutFile`andra argumentet, till ett heltal för att ange `createFileSet` hur `TRUE`många rader som ska skrivas till varje CSV-fil när den är .

I RStudio anger du följande kod:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Detta steg bör vara klar i ca 10 minuter.

När du `RxSpark` använder en `createFileSet` beräkningskontext har standardvärdet `TRUE` `rowsPerOutFile` och har ingen effekt. Om du vill skapa en enda CSV eller anpassa antalet rader `rxDataStep` per `local` fil, utför därför i en beräkningskontext (data kan fortfarande finnas i HDFS).

## <a name="final-steps"></a>Sista stegen

1. Rensa data. I RStudio anger du följande kod:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Stoppa fjärrparkprogrammet. I RStudio anger du följande kod:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Avsluta R-sessionen. I RStudio anger du följande kod:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört självstudien kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Information om hur du tar bort ett kluster finns i [Ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder R-funktioner i Apache Spark som körs på ett HDInsight Machine Learning-tjänstkluster. Mer information finns i följande artiklar:

* [Beräkningskontextalternativ för ett Azure HDInsight Machine Learning-tjänstkluster](r-server-compute-contexts.md)
* [R Funktioner för Spark på Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
