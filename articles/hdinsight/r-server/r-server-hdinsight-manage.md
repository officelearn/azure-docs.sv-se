---
title: Hantera R Server-kluster i HDInsight - Azure | Microsoft Docs
description: Lär dig hur du hanterar ett R Server-kluster i Azure HDInsight.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: c0a996555e35a99a6025e92bcb41fa192b18eece
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="manage-r-server-cluster-on-azure-hdinsight"></a>Hantera kluster för R Server på Azure HDInsight

I den här artikeln får lära du att hantera ett befintligt kluster R Server på Azure HDInsight för att utföra uppgifter som att lägga till multipel samtidiga användare, fjärransluta till en R Server (Microsoft ML Server) eller en klient, ändra kontexten för beräkning, osv.

## <a name="prerequisites"></a>Förutsättningar

* **R Server-kluster i HDInsight**: instruktioner finns i [komma igång med R Server på HDInsight](r-server-get-started.md).

* **En SSH-klient (Secure Shell)**: En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [använda SSH med HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Aktivera flera samtidiga användare

Du kan aktivera flera samtidiga användare för R Server-kluster i HDInsight genom att lägga till fler användare för kantnod som körs på gruppversion RStudio. När du skapar ett HDInsight-kluster måste du ange två användare, en HTTP-användare och en SSH-användare:

![Samtidig användare 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Användarnamn för klusterinloggning**: en HTTP-användare för autentisering via HDInsight-gatewayen som används till att skydda HDInsight-klustret du skapade. Den här HTTP-användaren används till att komma åt Ambari UI, YARN UI samt andra gränssnittskomponenter.
- **Secure Shell (SSH)-användarnamn**: en SSH-användare för åtkomst till klustret via SSH. Den här användaren är en användare i Linux-systemet för alla huvudnoder, arbetsnoder och kantnoder. Du kan därmed använda SSH för åtkomst till alla noder i ett fjärrkluster.

R Studio Server Community-versionen som används i R Server-kluster i HDInsight accepterar endast Linux-användarnamn och lösenord som en mekanism för inloggning. Du kan inte skicka token. När du försöker komma åt R Studio för första gången på en R Server-kluster, måste du logga in två gånger.

- Först logga in med HTTP-autentiseringsuppgifter via HDInsight-Gateway. 

- Sedan använda SSH-autentiseringsuppgifter för att logga in på RStudio.
  
För närvarande kan du bara skapa ett SSH-användarkonto när du etablerar ett HDInsight-kluster. Så om du vill ge flera användare åtkomst till R Server-kluster i HDInsight, måste du skapa ytterligare användare i Linux-system.

Eftersom RStudio körs på edge klusternod, finns det flera steg här:

1. Använd befintlig SSH-användare för att logga in till kantnoden
2. Lägg till fler Linux-användare på kantnoden
3. Använd RStudio Community-versionen med användaren som skapades

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Steg 1: Använd den SSH-användare du skapade och logga in på kantnoden

Följ anvisningarna på [Anslut till HDInsight (Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) åtkomst till kantnoden. Edge nod-adressen för R Server-kluster i HDInsight är `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Steg 2: Lägg till fler Linux-användare på kantnoden

Kör följande kommandon när du ska lägga till en användare vid kantnoden:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user 
    sudo passwd <yournewusername>

Följande skärmbild visar utdata.

![Samtidig användare 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

När du uppmanas att ange det aktuella Kerberos-lösenordet trycker du bara på **Retur**. Alternativet `-m` i kommandot `useradd` innebär att systemet skapar en arbetsmapp för användaren, vilket krävs för RStudio Comunity-versionen.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Steg 3: Använd RStudio Community-versionen med användaren som skapades

Åtkomst till RStudio från https://CLUSTERNAME.azurehdinsight.net/rstudio/. Om du loggar in för första gången efter att skapa klustret, anger du klustret administratörsautentiseringsuppgifter följt av SSH-autentiseringsuppgifter som du nyss skapade. Om detta inte är din första inloggning endast ange autentiseringsuppgifterna för SSH-användare som du skapade.

Du kan också samtidigt logga in med de ursprungliga autentiseringsuppgifterna (som standard *sshuser*) från ett annat webbläsarfönster.

Observera också att de nya användarna inte har rotbehörighet i Linux-systemet, men att de har samma åtkomst till alla filer i HDFS- och WASB-fjärrlagringen.

## <a name="connect-remotely-to-microsoft-ml-server-or-client"></a>Fjärransluta till Microsoft ML Server eller klient

Du kan konfigurera åtkomst till HDInsight Hadoop Spark beräknings-kontexten från en fjärrinstans av Microsoft ML Server eller Microsoft ML Client körs på skrivbordet. Om du vill göra det, måste du ange alternativen (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches och sshProfileScript) när definierar RxSpark compute kontext på skrivbordet: till exempel:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Mer information finns i avsnittet ”med hjälp av Microsoft R Server som ett Hadoop-klient” i [skapar en Compute kontext för Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Använda en beräkningskontext

Med en beräkningskontext kan du kontrollera om beräkningen utförs lokalt på kantnoden eller om den ska distribueras mellan noderna i HDInsight-klustret.

1. Från RStudio Server eller R-konsolen (i en SSH-session) använder du följande kod till att läsa in exempeldata till standardlagringen för HDInsight:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
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

2. Därefter skapar vissa data info och definierar två datakällor så att vi kan arbeta med data.

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

3. Kör en logistic regression över data med lokala beräknings-kontext.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Du bör se utdata som slutar med rader som liknar följande:

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

4. Kör den samma logistic regression med Spark-kontext. Spark-kontexten distribuerar bearbetningen mellan alla arbetsnoder i HDInsight-klustret.

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


   > [!NOTE]
   > Du kan också använda MapReduce för att distribuera beräkning över klusternoder. Mer information om beräkningskontexter finns i [Alternativ för beräkningskontexter för R Server på HDInsight](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuera R-kod till flera noder

Med R Server på HDInsight kan du ta befintliga R-koden och kör den över flera noder i klustret med hjälp av `rxExec`. Det här är praktiskt när du gör en parameterrensning eller simuleringar. Här följer ett kodexempel på hur du kan använda `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Om du fortfarande använder Spark- eller MapReduce-kontexten returnerar det här kommandot nodnamnet för de arbetsnoder som koden `(Sys.info()["nodename"])` kördes på. I ett kluster med fyra noder kan du till exempel få utdata som liknar följande:

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="access-data-in-hive-and-parquet"></a>Få åtkomst till data i Hive och Parquet

Med en funktion i R Server 9.1 kan du få direktåtkomst till data i Hive och Parquet och använda dem i ScaleR-funktioner i Spark-beräkningskontexten. Dessa funktioner är tillgängliga via nya ScaleR-datakällafunktioner som heter RxHiveData och RxParquetData som fungerar med Spark SQL för att läsa in data direkt till en Spark DataFrame för analys av ScaleR.  

Här är exempelkod där de nya funktionerna används:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


För ytterligare information om användning av dessa nya funktioner finns i direkthjälpen i ML-Server med hjälp av den `?RxHivedata` och `?RxParquetData` kommandon.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Installera ytterligare R-paket i klustret

### <a name="to-install-r-packages-on-the-edge-node"></a>Att installera R-paket på kantnoden

Om du vill installera ytterligare R-paket på kantnoden, kan du använda `install.packages()` inifrån R-konsolen när anslutna direkt till kantnoden via SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Att installera R-paket på arbetsnoden

Om du vill installera R-paket på worker-noder i klustret måste du använda en skriptåtgärd. Skriptåtgärder är bash-skript som används till att göra konfigurationsändringar i HDInsight-klustret eller till att installera ytterligare programvara, som fler R-paket. 

> [!IMPORTANT]
> Det går bara att använda skriptåtgärder för att installera ytterligare R-paket när klustret har skapats. Använd inte den här proceduren när du skapar klustret eftersom en förutsättning för skriptet är att R Server är helt installerat och konfigurerat.
>
>

1. Följ stegen i [anpassa kluster med skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md).

3. För **skicka skriptåtgärden**, ange följande information:

   * För **Skripttyp**väljer **anpassad**.

   * För **namn**, ange ett namn för skriptåtgärden.

    * För **Bash skript URI**, ange `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Det här är de skript som installerar ytterligare R-paket på arbetsnoden

   * Markera kryssrutan för **Worker**.

   * **Parametrar**: R-paketen som ska installeras. Till exempel, `bitops stringr arules`

   * Markera kryssrutan för att **spara den här skriptåtgärden**.  

   > [!NOTE]
   > 1. Som standard installeras alla R-paket från en ögonblicksbild av Microsoft MRAN-lagringsplatsen och matchar versionen av R Server som har installerats. Om du vill installera nyare version av paket finns en risk för kompatibilitetsproblem. Den här typen av installation är däremot möjlig om du anger `useCRAN` som det första elementet i paketlistan, till exempel `useCRAN bitops, stringr, arules`.  
   > 2. För vissa R-paket krävs ytterligare Linux-bibliotek. Av praktiska skäl har vi förinstallerat de beroenden som krävs av de 100 populäraste R-paketen. Men om R-paket som du installerar kräver bibliotek utöver dessa måste du ladda ned basskriptet som används här och lägga till steg för att installera systembiblioteken. Sedan måste du överföra de ändrade skripten till en offentlig blobbehållare i Azure Storage och använda det ändrade skriptet för att installera paketen.
   >    Mer information om hur du utvecklar skriptåtgärder finns i [Skriptåtgärdsutveckling](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Lägga till en skriptåtgärd](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Välj **Skapa** för att köra skriptet. När skriptet är färdigt är R-paketen tillgängliga på alla arbetsnoder.

## <a name="next-steps"></a>Nästa steg

* [Operationalisera R Server-kluster i HDInsight](r-server-operationalize.md)
* [Compute-kontexten alternativ för R Server-kluster i HDInsight](r-server-compute-contexts.md)
* [Azure lagringsalternativ för R Server-kluster i HDInsight](r-server-storage.md)
