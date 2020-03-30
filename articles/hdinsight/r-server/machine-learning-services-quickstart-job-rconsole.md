---
title: 'Snabbstart: R-skript på ML Services & R-konsol - Azure HDInsight'
description: I snabbstarten kör du ett R-skript på ett ML Services-kluster i Azure HDInsight med R-konsolen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: af41a7569dedc9a56f67be8ae791d7185e0c7489
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241593"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Snabbstart: Kör ett R-skript på ett ML Services-kluster i Azure HDInsight med R-konsolen

ML Services på Azure HDInsight tillåter R-skript att använda Apache Spark och Apache Hadoop MapReduce för att köra distribuerade beräkningar. ML Services styr hur anrop körs genom att ange beräkningskontexten. Kantnoden i ett kluster är en praktisk plats för att ansluta till klustret och köra dina R-skript. Med en kantnod har du möjlighet att köra de parallelliserade distribuerade funktionerna i RevoScaleR över kärnorna på kantnodservern. Du kan också köra dem över noderna i klustret med hjälp av RevoScaleR:s Hadoop Map Reduce- eller Apache Spark-beräkningskontexter.

I den här snabbstarten får du lära dig hur du kör ett R-skript med R-konsol som demonstrerar med Spark för distribuerade R-beräkningar. Du definierar en beräkningskontext för att utföra beräkningar lokalt på en kantnod och distribueras igen över noderna i HDInsight-klustret.

## <a name="prerequisites"></a>Krav

* Ett ML Services-kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **ML Services** för **klustertyp**.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Ansluta till R-konsolen

1. Anslut till kantnoden för ett ML Services HDInsight-kluster med SSH. Redigera kommandot nedan `CLUSTERNAME` genom att ersätta med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. Starta R-konsolen från SSH-sessionen med följande kommando:

    ```
    R
    ```

    Du bör se utdata med versionen av ML Server samt annan information.


## <a name="use-a-compute-context"></a>Använda en beräkningskontext

1. Från frågan `>` kan du ange R-kod. Använd följande kod för att läsa in exempeldata i standardlagringen för HDInsight:

    ```R
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Det här steget kan ta cirka 10 minuter att slutföra.

1. Skapa viss datainformation och definiera två datakällor. Ange följande kod i R-konsolen:

    ```R
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Kör en logistisk regression över data med hjälp av den **lokala** beräkningskontexten. Ange följande kod i R-konsolen:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Beräkningarna bör slutföras om ca 7 minuter. Du bör se utdata som slutar med rader som liknar följande kodavsnitt:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Kör samma logistiska regression med **spark-kontexten.** Spark-kontexten distribuerar bearbetningen mellan alla arbetsnoder i HDInsight-klustret. Ange följande kod i R-konsolen:

    ```R
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    Beräkningarna bör slutföras om ca 5 minuter.

1. Om du vill avsluta R-konsolen, använder du följande kommando:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Information om hur du tar bort ett kluster finns i [Ta bort ett HDInsight-kluster med webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig köra ett R-skript med R-konsol som demonstrerade med Spark för distribuerade R-beräkningar.  Gå vidare till nästa artikel om du vill lära dig vilka alternativ som är tillgängliga för att ange om och hur körningen parallelliseras mellan kärnorna i kantnoden eller HDInsight-klustret.

> [!div class="nextstepaction"]
>[Beräkna kontextalternativ för ML-tjänster på HDInsight](./r-server-compute-contexts.md)