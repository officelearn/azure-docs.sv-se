---
title: "Komma igång med R Server i HDInsight | Microsoft Docs"
description: "Lär dig att skapa en Apache Spark på HDInsight-kluster som innehåller R Server och sedan skicka ett R-skript på klustret."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f816a6972c0e80c6a7063705917ecf18debc75f6
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-using-r-server-on-hdinsight"></a>Kom igång med R Server på HDInsight
I HDInsight finns ett R Server-alternativ som ska integreras i HDInsight-klustret. Det gör att R-skript kan använda Spark och MapReduce för att köra distribuerade beräkningar. I det här dokumentet får du lära dig att skapa en R Server på HDInsight-kluster och sedan köra ett R-skript som visar hur du använder Spark för distribuerade R-beräkningar.

## <a name="prerequisites"></a>Krav
* **En Azure-prenumeration**: Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Mer information finns i [Get a Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Få en kostnadsfri utvärderingsversion av Azure).
* **En SSH-klient (Secure Shell)**: En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

  * **SSH-nycklar (valfritt)**: Du kan skydda det SSH-konto som används för att ansluta till klustret med ett lösenord eller en offentlig nyckel. Det är enklare att använda ett lösenord, och det gör att du kan komma igång utan att behöva skapa ett offentligt/privat nyckelpar. Det är dock säkrare att använda en nyckel.

      Stegen i det här dokumentet förutsätter att du använder ett lösenord.

### <a name="access-control-requirements"></a>Åtkomstkontrollkrav
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>Skapa klustret
> [!NOTE]
> I den här dokumentguiden får du stegvisa instruktioner om hur du skapar en R Server på HDInsight-kluster med information om grundkonfiguration. Mer information om andra inställningar för klusterkonfiguration (som att lägga till ytterligare Storage-konton, använda ett Azure Virtual Network eller skapa ett metaarkiv för Hive) finns i [Create Linux-based HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Skapa Linux-baserade HDInsight-kluster). Om du vill skapa en R Server med en mall för Azure-resurshantering kan du läsa [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Distribuera ett R Server HDInsight-kluster).
>
>

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **NEW** (NYTT), **Information + analys** och sedan **HDInsight**.

    ![Bild som visar hur du skapar ett nytt kluster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. I **Snabbregistrering** anger du ett namn på klustret i fältet **Klusternamn**. Om du har flera Azure-prenumerationer använder du posten **Prenumeration** för att välja den du vill använda.

    ![Val av klustrets namn och prenumeration](./media/hdinsight-getting-started-with-r/clustername.png)

4. Välj **Klustertyp** för att öppna bladet **Klusterkonfiguration**. På bladet **Klusterkonfiguration** väljer du följande alternativ:

   * **Klustertyp**: R Server
   * **Version**: välj vilken version av R Server som ska installeras i klustret. Välj den senaste versionen för att få de senaste funktionerna. Andra versioner är tillgängliga om det behövs för kompatibilitet. Viktig information för alla tillgängliga versioner finns [här](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes).
   * **R Studio community edition för R Server**: denna webbläsarbaserade IDE installeras som standard på gränsnoden.  Om du föredrar att inte installera avmarkerar du kryssrutan. Om du väljer att installera finns URL-adressen till RStudio Server-inloggning på ett portalprogramblad för klustret när det har skapats.

   Låt standardvärdena stå kvar och spara klustertypen med knappen **Välj**.

   ![Skärmbild av klustertypblad](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

5. Ange ett **inloggningsnamn** och **inloggningslösenord** för klustret.

   Ange ett **SSH-användarnamn**.  SSH används för att fjärransluta till klustret med en **Secure Shell-klient (SSH)**. Du kan antingen ange SSH-användare i den här dialogrutan eller när klustret har skapats (fliken Konfiguration för klustret). R Server är konfigurerat för att förvänta sig **SSH-användarnamnet** ”remoteuser”.  **Om du använder ett annat användarnamn måste du utföra ytterligare ett steg när klustret skapas.**

   Låt kryssrutan **Använd samma lösenord som klusterinloggning** vara markerad för att använda **LÖSENORD** som autentiseringstyp om du inte föredrar att använda en offentlig nyckel.  Du behöver ett offentligt/privat nyckelpar om du vill ha åtkomst till R Server på klustret via en fjärrklient, till exempel RTVS, RStudio eller någon annan skrivbords-IDE. Du måste välja ett SSH-lösenord om du installerar RStudio Server community edition.     

   Om du vill skapa och använda ett offentligt/privat nyckelpar avmarkerar du **Använd samma lösenord som klusterinloggning** och markerar sedan **OFFENTLIG NYCKEL** och fortsätter enligt informationen nedan.  Anvisningarna förutsätter att du har Cygwin med ssh-keygen eller motsvarande installerat.

   * Generera ett offentligt/privat nyckelpar från kommandotolken på den bärbara datorn:

   `ssh-keygen -t rsa -b 2048`

   * Följ instruktionen för att namnge en nyckelfil och ange sedan en lösenfras för ökad säkerhet. Skärmen bör se ut ungefär så här:

   ![SSH-kommandorad i Windows](./media/hdinsight-getting-started-with-r/sshcmdline.png)

   * Här skapar du en privat och en offentlig nyckelfil under namnet <private-key-filename>.pub, till exempel furiosa och furiosa.pub.

   ![SSH-katalog](./media/hdinsight-getting-started-with-r/dir.png)

   * Ange sedan den offentliga nyckelfilen (*.pub) när du tilldelar autentiseringsuppgifter för HDI-klustret och bekräfta slutligen din resursgrupp och din region och välj **Nästa**

   ![Bladet Autentiseringsuppgifter](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * Ändra behörigheter för den privata nyckelfilen på din bärbara dator

   `chmod 600 <private-key-filename>`

   * Använd den privata nyckelfilen för SSH för fjärrinloggning

   `ssh –i <private-key-filename> remoteuser@<hostname public ip>`

   eller som en del av definitionen av din Hadoop Spark-beräkningskontext för R Server på klienten (läs Using Microsoft R Server as a Hadoop Client (Använda Microsoft R Server som Hadoop-klient)) i avsnittet [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Skapa en beräkningskontext för Spark) i dokumentet [Get started with SacaleR on Apache Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) (Kom igång med SacaleR på Apace Spark.)

6. Via snabbstarten kommer du till bladet **Storage** där du kan välja Storage-kontots inställningar som ska användas för HDFS-filsystemets primära plats för klustret. Välj antingen ett nytt eller ett befintligt Azure Storage-konto eller Data Lake-lagringskonto.

   1. Om du väljer ett Azure Storage-konto kan du välja ett befintligt lagringskonto genom att välja **Välj ett lagringskonto** och sedan markera kontot. Du kan också skapa ett nytt konto med länken **Skapa ny** i avsnittet **Välj ett lagringskonto**.

      > [!NOTE]
      > Om du väljer **New** (Nytt) måste du ange ett namn för det nya lagringskontot. En grön bock visas om namnet är godkänt.

      **Standardbehållaren** får som standard klustrets namn. Lämna det här värdet.

      Om ett nytt kontolagringsalternativ valdes visas en uppmaning om att välja **Plats** för att välja region för att skapa lagringskontot.  

         ![Bladet Datakälla](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > När du väljer plats för standarddatakällan ställs även platsen för HDInsight-klustret in. Klustret och standarddatakällan måste vara i samma region.

   2. Om du väljer att använda ett befintligt Data Lake Store väljer du vilket ADLS-lagringskonto du ska använda och lägger till klustrets ADD-identitet för att tillåta åtkomst till Data Lake Store. Mer information om den här processen finns i [Creating HDInsight cluster with Data Lake Store using Azure portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) (Skapa HDInsight-kluster med Data Lake Store med Azure Portal).

   Spara konfigurationen av datakällan med knappen **Välj**.


7. **Sammanfattningsbladet** visas sedan för att verifiera dina inställningar. Här kan du ändra **klusterstorlek** för att ändra antalet servrar i klustret och även ange eventuella **skriptåtgärder** som ska köras. Om du inte vet att du behöver ett större kluster ska du lämna antalet arbetarnoder på standardinställningen `4`. Den uppskattade kostnaden för klustret visas på bladet.

   ![klustersammanfattning](./media/hdinsight-getting-started-with-r/clustersummary.png)

   > [!NOTE]
   > Om du vill kan du återställa storleken på klustret senare via portalen (Kluster -> Inställningar -> Skala kluster) för att öka eller minska antalet arbetarnoder.  Det är användbart för att försätta klustret i viloläge när det inte används eller för att lägga till kapacitet för att klara behovet för större uppgifter.
   >
   >

    Vissa faktorer att tänka på när du ändrar storlek på klustret, datanoderna och gränsnoden är:  

   * Prestanda för distribuerade R Server-analyser i Spark är proportionella i förhållande till antalet arbetarnoder vid stora datamängder.  

   * Prestanda för R Server-analyser är linjära när det gäller storleken på data som analyseras. Exempel:  

     * För små till mycket små data blir prestanda bäst när data analyseras i en lokal beräkningskontext på gränsnoden.  Mer information om i vilka scenarier logisk eller Spark-beräkningskontext fungerar bäst finns i Alternativ för beräkningskontexter för R Server på HDInsight.<br>
     * Om du loggar in på gränsnoden och kör R-skriptet körs alla utom ScaleR rx-funktionerna <strong>lokalt</strong> på gränsnoden så att minnet och antalet kärnor i gränsnoden storleksanpassas. Samma sak gäller om du använder R Server på HDI som fjärrberäkningskontext från datorn.

     ![Bladet Nodprisnivåer](./media/hdinsight-getting-started-with-r/pricingtier.png)

     Spara konfigurationen av nodpris med knappen **Välj**.

   Du kommer att se att det även finns en länk till **Ladda ned mall och parametrar**. Om du klickar på länken visas skript som kan användas för att automatisera skapandet av ett kluster med den valda konfigurationen. De här skripten är också tillgängliga från Azure Portal för klustret när det har skapats.

   > [!NOTE]
   > Det tar lite tid för klustret att skapas, vanligen cirka 20 minuter. Använd ikonen på startsidan eller posten **Meddelanden** till vänster på sidan när du vill se skapandeförloppet.
   >
   >

## <a name="connect-to-rstudio-server"></a>Anslut till RStudio Server

Om du har valt att ta med RStudio Server community edition i din installation kan du logga in på RStudio med två olika metoder.

1. Antingen genom att gå till följande URL-adress (där **KLUSTERNAMN** är namnet på klustret du har skapat):

    https://**KLUSTERNAMN**.azurehdinsight.net/rstudio/

2. Eller genom att öppna klusterposten i Azure Portal, markera snabblänken till R Server-instrumentpaneler och sedan välja R Studio-instrumentpanelen:

     ![Få åtkomst till R Studio-instrumentpanelen](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Få åtkomst till R Studio-instrumentpanelen](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > Vilken metod du än väljer måste du autentisera två gånger när du loggar in för första gången.  Vid den första autentiseringen anger du klusteradministratörens användar-ID och lösenord. Den andra gången anger du användar-ID och lösenord för SSH. Efterföljande inloggningar kräver endast SSH-lösenord och användar-ID.

## <a name="connect-to-the-r-server-edge-node"></a>Ansluta till R Server-gränsnoden
Anslut till R Server-gränsnoden för HDInsight-klustret med SSH:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Du kan också hitta `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`-adressen i Azure Portal om du markerar klustret och sedan väljer **Alla inställningar**, **Appar** och **RServer**. Då visas SSH-slutpunktsinformation för gränsnoden.
>
> ![Avbildning av SSH-slutpunkten för gränsnoden](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
>

Om du skyddat SSH-användarkontot med lösenord uppmanas du att ange det. Om du använde en offentlig nyckel kan du behöva använda `-i`-parametern för att ange motsvarande privata nyckel. Till exempel `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`.

Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

När du är ansluten får du en uppmaning som liknar följande.

`username@ed00-myrser:~$`

## <a name="use-the-r-console"></a>Använda R-konsolen

1. Från SSH-sessionen använder du följande kommando för att starta R-konsolen.  

   ```
   R

   You will see output similar to the following.
   R version 3.2.2 (2015-08-14) -- "Fire Safety"
   Copyright (C) 2015 The R Foundation for Statistical Computing
   Platform: x86_64-pc-linux-gnu (64-bit)

   R is free software and comes with ABSOLUTELY NO WARRANTY.
   You are welcome to redistribute it under certain conditions.
   Type 'license()' or 'licence()' for distribution details.

   Natural language support but running in an English locale

   R is a collaborative project with many contributors.
   Type 'contributors()' for more information and
   'citation()' on how to cite R or R packages in publications.

   Type 'demo()' for some demos, 'help()' for on-line help, or
   'help.start()' for an HTML browser interface to help.
   Type 'q()' to quit R.

   Microsoft R Server version 8.0: an enhanced distribution of R
   Microsoft packages Copyright (C) 2016 Microsoft Corporation

   Type 'readme()' for release notes.
   >
   ```

2. Från frågan `>` kan du ange R-kod. R Server innehåller paket som gör att du enkelt kan interagera med Hadoop och köra distribuerade beräkningar. Du kan exempelvis använda följande kommando för att visa roten för standardfilsystemet för HDInsight-klustret.

`rxHadoopListFiles("/")`

Du kan också använda adressering i WASB-format.

`rxHadoopListFiles("wasbs:///")`

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Använda R Server på HDI från en fjärrinstans av Microsoft R Server eller Microsoft R Client
Enligt avsnittet ovan angående användning av offentliga/privata nyckelpar för att få åtkomst till klustret är det möjligt att konfigurera åtkomst till HDI Hadoop Spark-beräkningskontexten från en fjärrinstans av Microsoft R Server eller Microsoft R Client som körs på ett skrivbord eller bärbar dator (läs Using Microsoft R Server as a Hadoop Client (Använda Microsoft R Server som Hadoop-klient)) i avsnittet [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Skapa en beräkningskontext för Spark) i [kom igång-onlineguiden för RevoScaleR Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started))).  För att göra det måste du ange följande alternativ när du definierar RxSpark-beräkningskontext på datorn: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches och sshProfileScript. Exempel:

```
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
```


## <a name="use-a-compute-context"></a>Använda en beräkningskontext
Med en beräkningskontext kan du kontrollera om beräkningen kommer att göras lokalt på gränsnoden eller om den kommer att distribueras på noderna i HDInsight-klustret.

1. Från RStudio Server eller R-konsolen (i en SSH-session) använder du följande för att läsa in exempeldata till HDInsights standardlagring.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

2. Nu ska vi skapa lite datainfo och definiera två datakällor så att vi kan arbeta med data.

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

3. Vi kör en logistisk regression mot data med den lokala beräkningskontexten.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Du bör se utdata som slutar med rader som liknar följande.

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

4. Sedan kör vi samma logistiska regression med Spark-kontext. Spark-kontexten distribuerar bearbetningen över alla arbetarnoder i HDInsight-klustret.

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
   > Du kan också använda MapReduce för att distribuera beräkning över klusternoder. Mer information om beräkningskontexter finns i [Alternativ för beräkningskontexter för R Server på HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuera R-kod till flera noder
Med R Server kan du enkelt ta befintlig R-kod och köra den mot flera noder i klustret med `rxExec`. Det är praktiskt när du gör en parameterrensning eller simuleringar. Här följer ett exempel på hur du kan använda `rxExec`.

`rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )`

Om du fortfarande kör Spark- eller MapReduce-kontext returnerar detta värdet för nodnamnet för de arbetarnoder som koden `(Sys.info()["nodename"])` kördes på. För exempelvis ett kluster med fyra noder kan du få utdata som liknar följande.


    ```
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
    ```

## <a name="accessing-data-in-hive-and-parquet"></a>Få åtkomst till data i Hive och Parquet
En ny funktion som är tillgänglig i R Server 9.0 och senare ger direktåtkomst till data i Hive och Parquet som kan användas av ScaleR-funktioner i Spark-beräkningskontexten. Dessa funktioner är tillgängliga via nya ScaleR-datakällafunktioner som heter RxHiveData och RxParquetData som fungerar med Spark SQL för att läsa in data direkt till en Spark DataFrame för analys av ScaleR.  

Följande visar exempelkod vid användning av de nya funktionerna:



    ```
    #..create a Spark compute context

    myHadoopCluster <- rxSparkConnect(reset = TRUE)
    ```


    ```
    #..retrieve some sample data from Hive and run a model

    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)
    ```

    ```
    #..retrieve some sample data from Parquet and run a model

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
    ```


    ```
    #..check on Spark data objects, cleanup, and close the Spark session

    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)
    ```

Mer info om hur du använder de nya funktionerna finns i onlinehjälpen i R Server via kommandona ?RxHivedata och ?RxParquetData.  


## <a name="install-r-packages"></a>Installera R-paket
Om du vill installera ytterligare R-paket på gränsnoden kan du använda `install.packages()` direkt ifrån R-konsolen när du är ansluten till gränsnoden via SSH. Om du behöver installera R-paket på klustrets arbetarnoder måste du emellertid använda en skriptåtgärd.

Skriptåtgärder är bash-skript som används för att göra konfigurationsändringar i HDInsight-klustret eller för att installera ytterligare programvara. I det här fallet för att installera fler R-paket. Om du vill installera ytterligare paket med en skriptåtgärd gör du enligt följande.

> [!IMPORTANT]
> Det går bara att använda skriptåtgärder för att installera ytterligare R-paket när klustret har skapats. Det bör inte användas när klustret skapas, eftersom skriptet förlitar sig på att R Server är helt installerat och konfigurerat.
>
>

1. Från [Azure Portal](https://portal.azure.com) väljer du din R Server på HDInsight-klustret.

2. Från bladet **Inställningar** väljer du **Skriptåtgärder** och sedan **Skicka ny** för att skicka en ny skriptåtgärd.

   ![Bild på bladet med skriptåtgärder](./media/hdinsight-getting-started-with-r/scriptaction.png)

3. Från bladet **Skicka skriptåtgärd** anger du följande information.

   * **Namn**: Ett eget namn som identifierar det här skriptet

   * **Bash-skript-URI**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Huvud**: Det här ska vara **omarkerat**

   * **Arbetare**: Det här ska vara **markerat**

   * **Edge-noder**: Det här ska vara **omarkerat**.

   * **Zookeeper**: Detta ska vara **avmarkerat**

   * **Parametrar**: R-paketen som ska installeras. Till exempel, `bitops stringr arules`

   * **Persist this script...**(Spara det här skriptet
): Det ska vara **markerat**  

   > [!NOTE]
   > 1. Som standard installeras alla R-paket från en ögonblicksbild av Microsoft MRAN-lagringsplatsen och matchar versionen av R Server som har installerats.  Om du vill installera nyare versioner av paket finns det viss risk för inkompatibilitet, men det är möjligt om du anger `useCRAN` som första element av paketlistan, till exempel `useCRAN bitops, stringr, arules`.  
   > 2. Vissa R-paket kräver ytterligare Linux-bibliotek. Av praktiska skäl har vi förinstallerat de beroenden som krävs av de 100 populäraste R-paketen. Men om R-paket som du installerar kräver bibliotek utöver dessa måste du ladda ned basskriptet som används här och lägga till steg för att installera systembiblioteken. Sedan måste du överföra de ändrade skripten till en offentlig blobbehållare i Azure Storage och använda det ändrade skriptet för att installera paketen.
   >    Mer information om hur du utvecklar skriptåtgärder finns i [Skriptåtgärdsutveckling](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Lägga till en skriptåtgärd](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Välj **Skapa** för att köra skriptet. När skriptet är klart är R-paketen tillgängliga på alla arbetarnoder.

## <a name="using-microsoft-r-server-operationalization"></a>Använda Microsoft R Server-driftsättningen
När datamodelleringen är klar kan du operationalisera modellen för att göra förutsägelser. Utför nedanstående steg för att konfigurera Microsoft R Server-operationalisering.

Först ssh till gränsnoden. Till exempel ```ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net```.

När du har använt ssh byter du katalog till följande katalog och använder sudo för dotnet dll som det beskrivs nedan.

```
   cd /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil
   sudo dotnet Microsoft.DeployR.Utils.AdminUtil.dll
```

Gör följande för att konfigurera en Microsoft R Server-operationalisering i en enda konfiguration:

* Välj ”1. Configure R Server for Operationalization” (Konfigurera R-server för driftsättning)
* Välj ”A. One-box (web + compute nodes)” (En konfiguration (webb- och beräkningsnoder))
* Ange ett lösenord för **adminanvändaren**

![en enda driftsättning](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Du kan utföra diagnostiska kontroller som ett valfritt steg genom att köra diagnostiska test som visas nedan.

* Välj ”6. Run diagnostic tests” (Kör diagnostiktest)
* Välj ”A. Testkonfiguration”
* Ange användarnamn = "admin" och lösenordet från ovanstående konfigurationssteg
* Bekräfta övergripande hälsa = skicka
* Avsluta admin-verktyget
* Avsluta SSH

![Diagnostik för driftsättning](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

I det här skedet är konfigurationen för driftsättning klar. Nu kan du använda paketet ”mrsdeploy” på din RClient för att ansluta till driftsättningen på gränsnoden och börja använda dess funktioner som [fjärrkörning](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) och [webbtjänster](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). Beroende på om klustret är konfigurerat på ett virtuellt nätverk eller inte kan du behöva konfigurera portvidarebefordran via SSH-inloggning, vilket förklaras nedan:

### <a name="rserver-cluster-on-virtual-network"></a>RServer-kluster i ett virtuellt nätverk

Kontrollera att du tillåter trafik genom port 12800 till gränsnoden. På så sätt kan du använda gränsnoden för att ansluta till driftsättningsfunktionen.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Om remoteLogin() inte kan ansluta till gränsnoden, men SSH till gränssnod fungerar, måste du verifiera om regeln för att tillåta trafik på port 12800 har ställts in på rätt sätt eller inte. Om du fortsätter att råka ut för problemet kan du använda en lösning genom att ställa in portvidarebefordran via SSH.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Inget RServer-kluster installerat i ett virtuellt nätverk

Om inget kluster har konfigurerats på vnet ELLER om du har problem med anslutningen via vnet kan du använda SSH-portvidarebefordran enligt nedan:

```
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Du kan även konfigurera det på Putty.

![putty ssh-anslutning](./media/hdinsight-hadoop-r-server-get-started/putty.png)

När din SSH-session är aktiv vidarebefordras trafiken från datorns port 12800 till gränsnodens port 12800 via SSH-session. Kontrollera att du använder `127.0.0.1:12800` i remoteLogin()-metoden. Du loggas då in till gränsnodens driftsättning via portvidarebefordran.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Hur skalar man beräkningsnoder för Microsoft R Server-driftsättning på HDInsight-arbetarnoder?


### <a name="decommission-the-worker-nodes"></a>Inaktivera arbetarnoder
Microsoft R Server hanteras för närvarande inte via Yarn. Om arbetarnoderna inte är inaktiverade fungerar inte Yarn-resurshanteraren som förväntat eftersom den inte känner till resurserna som tas upp av servern. För att undvika det rekommenderar vi att du inaktiverar arbetarnoderna dit du vill skala beräkningsnoderna.

Steg för att ta arbetarnoder ur drift:

* Logga in på HDI-klustrets Ambari-konsol och klicka på fliken ”värdar”
* Markera arbetarnoder (som ska inaktiveras, klicka på "Åtgärder" > "Selected Hosts" (Valda värdar) > "Värdar" > klicka på "Turn ON Maintenance Mode" (Aktivera underhållsläge). I bilden nedan har vi till exempel valt att inaktivera wn3 och wn4.  

   ![inaktivera arbetarnoder](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Välj "Åtgärder" > "Selected Hosts" (Valda värdar) > "DataNodes" > klicka på "Decommission" (Avställning)
* Välj "Åtgärder" > "Selected Hosts" (Valda värdar) > "NodeManagers" > klicka på "Decommission" (Avställning)
* Välj "Åtgärder" > "Selected Hosts" (Valda värdar) > "DataNodes" > klicka på "Stoppa"
* Välj "Åtgärder" > "Selected Hosts" (Valda värdar) > "NodeManagers" > klicka på "Stoppa"
* Välj "Åtgärder" > "Selected Hosts" (Valda värdar) > "Hosts" (Värdar) > klicka på "Stop All Components" (Stoppa alla komponenter)
* Avmarkera arbetarnoderna och markera huvudnoderna
* Välj "Åtgärder" > "Selected Hosts" (Valda värdar) > "Hosts" (Värdar) > klicka på "Restart All Components" (Starta om alla komponenter)


###    <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Konfigurera beräkningsnoder på varje inaktiverad arbetarnod

* SSH till varje inaktiverad arbetarnod
* Kör admin-verktyget med `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`
* Skriv "1" för att välja alternativ "1. Configure R Server for Operationalization” (Konfigurera R-server för driftsättning)
* Skriv "c" för att välja alternativ "C. ”Beräkningsnod”. Då konfigureras beräkningsnoden på arbetarnoden.
* Avsluta admin-verktyget

### <a name="add-compute-nodes-details-on-web-node"></a>Lägga till beräkningsnoder på webbnod
När alla inaktiverade arbetarnoder har konfigurerats för att köra beräkningsnoder återgår du till gränsnoden och lägger till inaktiverade arbetarnoders IP-adresser i Microsoft R Server-webbnodens konfiguration:

* SSH till gränsnod
* Kör `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`
* Leta efter ”URI”-avsnittet och lägg till arbetarnodens IP och portinformation.

![ta arbetarnoder ur drift, kommandorad](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur man skapar ett nytt HDInsight-kluster som innehåller Server och grunderna för att använda R-konsolen från en SSH-session kan du använda följande för att upptäcka andra sätt att arbeta med R Server på HDInsight.

* [Lägga till RStudio Server till HDInsight (om det inte installerades när klustret skapades)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Alternativ för beräkningskontexter för R Server på HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
* [Alternativ för Azure Storage för R Server på HDInsight](hdinsight-hadoop-r-server-storage.md)

