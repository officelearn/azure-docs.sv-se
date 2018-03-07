---
title: "Kom igång med R Server i HDInsight – Azure | Microsoft Docs"
description: "Lär dig att skapa en Apache Spark i ett HDInsight-kluster som innehåller R Server och sedan skicka ett R-skript i klustret."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.openlocfilehash: aa7f2e6f44036738756391ecaa265c57c093c42c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>Kom igång med R Server på HDInsight

I Azure HDInsight finns ett R Server-alternativ som ska integreras i HDInsight-klustret. Med det här alternativet kan R-skript använda Spark och MapReduce till att köra distribuerade beräkningar. I den här artikeln får du lära dig hur du skapar en R Server i ett HDInsight-kluster. Därefter lär du dig att köra ett R-skript som demonstrerar hur du använder Spark för distribuerade R-beräkningar.


## <a name="prerequisites"></a>Nödvändiga komponenter

* **En Azure-prenumeration**: Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Mer information finns i [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Få en kostnadsfri utvärderingsversion av Azure).
* **En SSH-klient (Secure Shell)**: En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).
* **SSH-nycklar (valfritt)**: Du kan skydda det SSH-konto som används för att ansluta till klustret med ett lösenord eller en offentlig nyckel. Det är enklare att använda ett lösenord, och det gör att du kan komma igång utan att behöva skapa ett offentligt/privat nyckelpar. Det är dock säkrare att använda en nyckel.

  > [!NOTE]
  > Stegen i den här artikeln förutsätter att du använder ett lösenord.


## <a name="automate-cluster-creation"></a>Skapa kluster automatiskt

Du kan skapa HDInsight R Server-instanser automatiskt med hjälp av Azure Resource Manager-mallar, SDK och PowerShell.

* Om du vill skapa en R Server-instans med en mall för Azure-resurshantering kan du läsa artikeln [Deploy an R server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Distribuera ett R Server HDInsight-kluster).
* Om du vill skapa en R Server med-instans med .NET SDK kan du läsa artikeln om att [skapa Linux-baserade kluster i HDInsight med hjälp av .NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Om du vill distribuera R Server med PowerShell läser du [Create Linux-based clusters in HDInsight using Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Skapa Linux-baserade kluster i HDInsight med Azure PowerShell).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>Skapa ett kluster med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **New** (Nytt) > **Information + analys** > **HDInsight**.

    ![Bild som visar hur du skapar ett nytt kluster](./media/r-server-get-started/newcluster.png)

3. I **Snabbregistrering** anger du ett namn på klustret i rutan **Klusternamn**. Om du har flera Azure-prenumerationer använder du rutan **Prenumeration** för att välja den du vill använda.

    ![Val av klustrets namn och prenumeration](./media/r-server-get-started/clustername.png)

4. Välj **Klustertyp** för att öppna fönstret **Klusterkonfiguration**. I rutan **Klusterkonfiguration** väljer du följande alternativ:

    * **Klustertyp**: Välj **R Server**.
    * **Version**: Välj vilken version av R Server som ska installeras i klustret. Den version som är tillgänglig just nu är **R Server 9.1 (HDI 3.6)**. Viktig information för tillgängliga versioner av R Server finns på [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).
    * **R Studio community edition för R Server**: Denna webbläsarbaserade IDE installeras som standard på gränsnoden. Om du föredrar att inte installera den avmarkerar du kryssrutan. Om du väljer att installera den finns URL-adressen till RStudio Server-inloggningen i ett portalprogramfönster för klustret när det har skapats.
    * Låt standardvärdena stå kvar och spara klustertypen med knappen **Välj**.

        ![Skärmbild av fönstret ”Klustertyp”](./media/r-server-get-started/clustertypeconfig.png)

5. I fönstret **Grundläggande inställningar** anger du ett användarnamn respektive lösenord för klustret i rutorna **Användarnamn för klusterinloggning** och **Lösenord för klusterinloggning**.

6. I rutan **Secure Shell (SSH)-användarnamn** anger du SSH-användarnamnet. SSH används för att fjärransluta till klustret med en SSH-klient. Du kan ange SSH-användaren i den här rutan eller när klustret har skapats (på fliken **Konfiguration** för klustret).
   
   > [!NOTE] 
   > R Server är konfigurerat för att förvänta sig SSH-användarnamnet ”remoteuser”. Om du använder ett annat användarnamn måste du utföra ytterligare ett steg när klustret har skapats.

7. Låt kryssrutan **Använd samma lösenord som klusterinloggning** vara markerad för att använda **LÖSENORD** som autentiseringstyp om du inte föredrar att använda en offentlig nyckel. Du behöver ett offentligt/privat nyckelpar för åtkomst till R Server i klustret via fjärrklient, som R Tools för Visual Studio, RStudio eller någon annan skrivbords-IDE. Om du installerar RStudio Server community edition måste du välja ett SSH-lösenord.     

   Om du vill skapa och använda ett offentligt/privat nyckelpar ska du avmarkera **Använd samma lösenord som klusterinloggning**. Välj sedan **OFFENTLIG NYCKEL** och fortsätt enligt följande. Anvisningarna förutsätter att du har Cygwin med ssh-keygen eller motsvarande installerat.

   a. Generera ett offentligt/privat nyckelpar från kommandotolken på den bärbara datorn:

        ssh-keygen -t rsa -b 2048

   b. Följ instruktionen för att namnge en nyckelfil och ange sedan en lösenfras för ökad säkerhet. Skärmen bör se ut ungefär så här:

      ![SSH-kommandorad i Windows](./media/r-server-get-started/sshcmdline.png)

      Med det här kommandot skapar du en privat och en offentlig nyckelfil med namnet <private-key-filename>.pub. I det här exemplet är filerna furiosa och furiosa.pub:

      ![Exempelresultat av kommandot dir](./media/r-server-get-started/dir.png)

   c. Ange filen för offentlig nyckel (&#42;.pub) när du tilldelar HDI-autentiseringsuppgifter för kluster. Bekräfta din resursgrupp och region och välj **Nästa**.

      ![Fönstret Autentiseringsuppgifter](./media/r-server-get-started/publickeyfile.png)  

   d. Så här ändrar du behörigheter för den privata nyckelfilen på din bärbara dator:

        chmod 600 <private-key-filename>

   e. Använd den privata nyckelfilen med SSH för fjärrinloggning:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      Du kan också ange filen för privat nyckel som en del av definitionen av Hadoop Spark-beräkningskontexten för R Server på klienten. Mer information finns i [Create a Compute Context for Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark) (Skapa en beräkningskontext för Spark).

8. Snabbregistreringen tar dig till fönstret **Storage**. Där väljer du lagringskontots inställningar som ska användas för HDFS-filsystemets primära plats som klustret använder. Välj ett nytt eller ett befintligt Azure Storage-konto eller Azure Data Lake-lagringskonto.

    - Om du väljer ett Azure Storage-konto kan du välja ett befintligt konto genom att välja **Välj ett lagringskonto** och sedan markera det relevanta kontot. Om du vill skapa ett nytt konto använder du länken **Skapa nytt** i avsnittet **Välj ett lagringskonto**.

      > [!NOTE]
      > Om du väljer att **skapa ett nytt** konto måste du ange ett namn för det nya lagringskontot. En grön bock visas om namnet är godkänt.

      **Standardbehållaren** får klustrets namn som standard. Lämna det här standardvärdet.

      När du har valt ett nytt lagringskonto använder du **Plats** i meddelandet för att välja en region för den.  

         ![Datakällsfönster](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > När du väljer plats för standarddatakällan ställs även platsen för HDInsight-klustret in. Klustret och standarddatakällan måste vara i samma region.

    - Om du vill använda ett befintligt Data Lake Store-konto väljer du vilket konto du vill använda. Lägg sedan till klustrets *ADD*-identitet till klustret för att tillåta åtkomst till lagret. Mer information om den här processen finns i [Creating HDInsight cluster with Data Lake Store using Azure portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) (Skapa HDInsight-kluster med Data Lake Store med Azure Portal).

    Spara konfigurationen av datakällan med knappen **Välj**.


9. Fönstret **Sammanfattning** visas sedan där du kan verifiera dina inställningar. Här kan du ändra klusterstorlek för att ändra antalet servrar i klustret. Du kan också ange skriptåtgärder du vill köra. Om du inte vet att du behöver ett större kluster ska du lämna antalet arbetsnoder på standardinställningen **4**. I fönstret ser du också klustrets uppskattade kostnad.

    ![Klustersammanfattning](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Om du vill kan du ändra storlek på klustret senare via portalen (**Kluster** > **Inställningar** > **Skala kluster**) för att öka eller minska antalet arbetsnoder. Sådan storleksändring kan användas till att försätta klustret i viloläge när det inte används eller för att lägga till kapacitet för större uppgifter.
   >
   >

   Faktorer att tänka på när du ändrar storlek på klustret, datanoderna och gränsnoden är:  

   * Prestanda för distribuerade R Server-analyser i Spark är proportionella i förhållande till antalet arbetsnoder vid stora datamängder.  

   * Prestanda för R Server-analyser är linjära när det gäller storleken på data som analyseras. Till exempel:  

     * För små till mycket små data blir prestanda bäst när data analyseras i en lokal beräkningskontext på gränsnoden. Mer information om i vilka scenarier logisk eller Spark-beräkningskontext fungerar bäst finns i [Alternativ för beräkningskontexter för R Server på HDInsight](r-server-compute-contexts.md).<br>
     * Om du loggar in på gränsnoden och kör ditt R-skript så körs alla funktioner förutom ScaleR rx *lokalt* på gränsnoden. Minnet och antalet kärnor för gränsnoden bör därför ändras motsvarande. Samma sak gäller om du använder R Server på HDI som fjärrberäkningskontext från datorn.

   Spara konfigurationen av nodpris med knappen **Välj**.

   ![Fönster för nodprisnivåer](./media/r-server-get-started/pricingtier.png)

   Det finns även en länk till att **ladda ned mall och parametrar**. Om du klickar på länken visas skript som kan användas för att automatisera skapandet av ett kluster med den valda konfigurationen. De här skripten är också tillgängliga från Azure Portal för klustret när det har skapats.

   > [!NOTE]
   > Det tar lite tid att skapa klustret, normalt cirka 20 minuter. Använd ikonen på startsidan eller posten **Meddelanden** till vänster på sidan när du vill se skapandeförloppet.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Anslut till RStudio Server

Om du har valt att ta med RStudio Server community edition i din installation kan du logga in på RStudio med två metoder:

- Gå till följande URL-adress (där *KLUSTERNAMN* är namnet på klustret du har skapat):

    https://*KLUSTERNAMN*.azurehdinsight.net/rstudio/

- Öppna klusterposten i Azure Portal, markera snabblänken till **R Server-instrumentpaneler** och välj sedan **R Studio-instrumentpanelen**:

  ![Få åtkomst till RStudio-instrumentpanelen](./media/r-server-get-started/rstudiodashboard1.png)

  ![Få åtkomst till RStudio-instrumentpanelen](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> Oavsett vilken metod du väljer måste du autentisera dig två gånger när du loggar in för första gången. Vid den första autentiseringen anger du klusteradministratörens användar-id och lösenord. Den andra gången anger du användar-ID och lösenord för SSH. Efterföljande inloggningar kräver endast användar-ID och lösenord för SSH.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Ansluta till R Server-kantnoden

Anslut till R Server-gränsnoden för HDInsight-klustret via SSH med följande kommando:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Du hittar `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`-adressen i Azure Portal. Välj ditt kluster och välj sedan **All Settings** (Alla inställningar
) > **Appar** > **RServer**. Då visas SSH-slutpunktsinformation för gränsnoden.
>
> ![Avbildning av SSH-slutpunkten för gränsnoden](./media/r-server-get-started/sshendpoint.png)
>
>

Om du skyddat SSH-användarkontot med lösenord uppmanas du att ange det. Om du använde en offentlig nyckel kan du behöva använda `-i`-parametern för att ange motsvarande privata nyckel. Till exempel:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Mer information finns i [Ansluta till HDInsight (Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

När du är ansluten får du en uppmaning som liknar följande:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Aktivera flera samtidiga användare

Du kan aktivera flera samtidiga användare genom att lägga till fler användare för kantnoden som RStudio community-versionen körs på.

När du skapar ett HDInsight-kluster måste du ange två användare, en HTTP-användare och en SSH-användare.

![Ange autentiseringsuppgifter för klusteranvändaren och SSH-användaren](./media/r-server-get-started/concurrent-users-1.png)

- **Användarnamn för klusterinloggning**: En HTTP-användare för autentisering via HDInsight-gatewayen som används till att skydda HDInsight-klustret du skapade. Den här HTTP-användaren används till att komma åt Ambari UI, YARN UI samt andra gränssnittskomponenter.
- **Secure Shell (SSH)-användarnamn**: En SSH-användare för åtkomst till klustret via SSH. Den här användaren är en användare i Linux-systemet för alla huvudnoder, arbetsnoder och kantnoder. Du kan därmed använda SSH för åtkomst till alla noder i ett fjärrkluster.

R Studio Server Community-versionen som används i Microsoft R Server på kluster av HDInsight-typ accepterar endast Linux-användarnamn och -lösenord som en mekanism för inloggning. Du kan inte skicka token. Så om du har skapat ett nytt kluster och vill använda R Studio för att komma åt det måst du logga in två gånger.

1. Logga in med HTTP-autentiseringsuppgifterna via HDInsight-gatewayen: 

    ![Ange HTTP-autentiseringsuppgifter](./media/r-server-get-started/concurrent-users-2a.png)

2. Använd SSH-autentiseringsuppgifterna till att logga in på RStudio:
  
    ![Ange SSH-autentiseringsuppgifter](./media/r-server-get-started/concurrent-users-2b.png)

För närvarande kan du bara skapa ett SSH-användarkonto när du etablerar ett HDInsight-kluster. Om du vill ge flera användare åtkomst till Microsoft R Server i HDInsight-kluster måste du skapa ytterligare användare i Linux-systemet.

Eftersom RStudio Server Community-versionen körs på klustrets gränsnod ingår tre steg i detta:

1. Använd den SSH-användare du skapade och logga in på gränsnoden.
2. Lägg till fler Linux-användare på gränsnoden.
3. Använd RStudio Community-versionen med användaren som skapades.

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Använd den SSH-användare du skapade och logga in på kantnoden

Ladda ned valfritt SSH-verktyg (till exempel PuTTY) och använd den befintliga SSH-användaren till att logga in. Följ instruktionerna i [Ansluta till HDInsight (Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) för åtkomst till kantnoden. Gränsnodsadressen till R Server i HDInsight-klustret är: **klusternamn-ed-ssh.azurehdinsight.net**


### <a name="add-more-linux-users-to-the-edge-node"></a>Lägga till fler Linux-användare på gränsnoden

Kör följande kommandon när du ska lägga till en användare vid gränsnoden:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Du bör se utdata som liknar följande: 

![Utdata för sudokommandon](./media/r-server-get-started/concurrent-users-3.png)

När du har ombetts ange det aktuella Kerberos-lösenordet väljer du bara RETUR för att ignorera det. Alternativet `-m` i kommandot `useradd` innebär att systemet skapar en arbetsmapp för användaren. Den här mappen krävs för community-versionen av RStudio.


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>Använd RStudio Community-versionen med användaren som skapades

Använd den skapade användaren för att logga in på RStudio:

![Inloggningssida för RStudio](./media/r-server-get-started/concurrent-users-4.png)

Observera att RStudio indikerar att du loggar in i klustret med den nya användaren (här till exempel **sshuser6**): 

![Platsen för den nya användaren i kommandofältet RStudio](./media/r-server-get-started/concurrent-users-5.png)

Du kan också samtidigt logga in med de ursprungliga autentiseringsuppgifterna (som standard **sshuser**) från ett annat webbläsarfönster.

Du kan skicka ett jobb med hjälp av ScaleR-funktioner. Här är ett exempel på kommandon som används till att köra ett jobb:

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
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

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

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


Lägg märke till att de jobb som skickas ligger under olika användarnamn i YARN-gränssnittet:

![Lista över användare i YARN-användargränssnittet](./media/r-server-get-started/concurrent-users-6.png)

Lägg även märke till att nyligen tillagda användare inte har rotprivilegier i Linux-system. Men de har samma åtkomst till alla filer i det fjärranslutna HDFS-filsystemet och Blob Storage.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Använda R-konsolen

1. Starta R-konsolen från SSH-sessionen med följande kommando:  

        R

2. Du bör se utdata som liknar följande:
    
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

3. Från frågan `>` kan du ange R-kod. R Server innehåller paket som gör att du enkelt kan interagera med Hadoop och köra distribuerade beräkningar. Du kan exempelvis använda följande kommando till att visa roten för standardfilsystemet för HDInsight-klustret:

        rxHadoopListFiles("/")

4. Du kan även använda Blob Storage-formatet för adressering:

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Använda R Server på HDI från en fjärrinstans av Microsoft R Server eller Microsoft R Client

Du kan konfigurera åtkomst till HDI Hadoop Spark-beräkningskontexten från en fjärrinstans av Microsoft R Server eller Microsoft R Client som körs på en stationär eller bärbar dator. Mer information finns i avsnittet ”Using Microsoft R Server as a Hadoop Client” (Använda Microsoft R Server som en Hadoop-klient) i artikeln [Create a Compute Context for Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) (Skapa en beräkningskontext för Spark). För att göra det måste du ange följande alternativ när du definierar RxSpark-beräkningskontexten i datorn: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches och sshProfileScript. Här är ett exempel:


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


## <a name="use-a-compute-context"></a>Använda en beräkningskontext

Med en beräkningskontext kan du kontrollera om beräkningen utförs lokalt på gränsnoden eller om den ska distribueras mellan noderna i HDInsight-klustret.

1. Från RStudio Server eller R-konsolen (i en SSH-session) använder du följande kod till att läsa in exempeldata till standardlagringen för HDInsight:

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
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

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Skapa lite datainfo och definiera två datakällor så att du kan arbeta med data:

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Kör en logistisk regression mot data med den lokala beräkningskontexten:

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

4. Kör samma logistiska regression med Spark-kontext. Spark-kontexten distribuerar bearbetningen mellan alla arbetsnoder i HDInsight-klustret.

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

Med R Server kan du enkelt ta befintlig R-kod och köra den mot flera noder i klustret med `rxExec`. Det här är praktiskt när du gör en parameterrensning eller simuleringar. Här följer ett kodexempel på hur du kan använda `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Om du fortfarande använder Spark- eller MapReduce-kontexten returnerar det här kommandot `nodename`-värdet för de arbetsnoder som koden `(Sys.info()["nodename"])` kördes på. I ett kluster med fyra noder kan du till exempel få utdata som liknar följande:

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

Med en funktion i R Server 9.1 kan du få direktåtkomst till data i Hive och Parquet och använda dem i ScaleR-funktioner i Spark-beräkningskontexten. Funktionerna är tillgängliga via de nya ScaleR-datakällsfunktionerna RxHiveData och RxParquetData. Funktionerna fungerar med Spark SQL och läser in data direkt i en Spark DataFrame för analys av ScaleR.  

Följande kod visar några exempel på hur de nya funktionerna ska användas:

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
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

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


Mer information om de här nya funktionerna finns i onlinehjälpen i R Server via kommandona `?RxHivedata` och `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Installera ytterligare R-paket på kantnoden

Om du vill installera ytterligare R-paket på gränsnoden kan du använda `install.packages()` direkt ifrån R-konsolen när du är ansluten till gränsnoden via SSH. Om du behöver installera R-paket på klustrets arbetsnoder måste du emellertid använda en skriptåtgärd.

Skriptåtgärder är bash-skript som används till att göra konfigurationsändringar i HDInsight-klustret eller till att installera ytterligare programvara, som fler R-paket. Om du vill installera ytterligare paket med en skriptåtgärd gör du så här.

> [!IMPORTANT]
> Du kan bara använda skriptåtgärder för att installera ytterligare R-paket när klustret har skapats. Använd inte den här proceduren när du skapar klustret eftersom en förutsättning för skriptet är att R Server är helt installerat och konfigurerat.
>
>

1. Från [Azure Portal](https://portal.azure.com) väljer du din R Server på HDInsight-klustret.

2. I fönstret **Inställningar** väljer du **Skriptåtgärder** > **Skicka ny**.

   ![Bild på sidan med skriptåtgärder](./media/r-server-get-started/scriptaction.png)

3. Från fönstret **Skicka skriptåtgärd** anger du följande information:

   * **Namn**: ett eget namn som identifierar det här skriptet.

   * **Bash-skript-URI**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Huvud**: det här alternativet ska vara avmarkerat.

   * **Arbete**: Det här objektet ska vara avmarkerat.

   * **Zookeeper**: Det här objektet ska vara avmarkerat.

   * **Edge-noder**: det här objektet ska vara markerat.

   * **Parametrar**: R-paketen som ska installeras, till exempel `bitops stringr arules`.

   * **Spara den här skriptåtgärden**: det här alternativet ska vara markerat.  

   > [!NOTE]
   > Som standard installeras alla R-paket från en ögonblicksbild av Microsoft R-programnätverkets lagringsplats och matchar den installerade versionen av R Server. Om du vill installera nyare version av paket finns en risk för kompatibilitetsproblem. Den här typen av installation är däremot möjlig om du anger `useCRAN` som det första elementet i paketlistan, till exempel `useCRAN bitops, stringr, arules`.  
   > 
   > För vissa R-paket krävs ytterligare Linux-bibliotek. Av praktiska skäl har vi förinstallerat de beroenden som krävs av de 100 populäraste R-paketen. Om R-paket som du installerar kräver bibliotek utöver dessa måste du ladda ned basskriptet som används här och lägga till steg för att installera systembiblioteken. Sedan måste du överföra de ändrade skripten till en offentlig blobbehållare i Azure Storage och använda det ändrade skriptet för att installera paketen.
   >
   > Mer information om hur du utvecklar skriptåtgärder finns i [Skriptåtgärdsutveckling](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Lägga till en skriptåtgärd](./media/r-server-get-started/submitscriptaction.png)

4. Välj **Skapa** för att köra skriptet. När skriptet är färdigt är R-paketen tillgängliga på alla arbetsnoder.


## <a name="configure-microsoft-r-server-operationalization"></a>Konfigurera Microsoft R Server-driftsättningen

När datamodelleringen är klar kan du driftsätta modellen för att göra förutsägelser. Utför följande steg när du ska konfigurera Microsoft R Server-driftsättning:

1. Använd kommandot `ssh` för gränsnoden – till exempel: 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. Ändra katalogen för den aktuella versionen och använda den kommandot `sudo dotnet` för DLL-filen. 

   Microsoft R-server 9.1:

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   Microsoft R-server 9.0:

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Gör så här när du ska konfigurera en Microsoft R Server-driftsättning i en enda konfiguration:

   a. Välj `Configure R Server for Operationalization`.

   b. Välj `A. One-box (web + compute nodes)`.

   c. Ange ett lösenord för `admin`-användaren.

   ![Driftsättning i en enda konfiguration](./media/r-server-get-started/admin-util-one-box-.png)

4. Som ett extra steg kan du följande diagnostiska test:

   a. Välj `6. Run diagnostic tests`.

   b. Välj `A. Test configuration`.

   c. Ange `admin` som användarnamn och ange lösenordet från föregående konfigurationssteg.

   d. Bekräfta `Overall Health = pass`.

   e. Avsluta admin-verktyget.

   f. Avsluta SSH.

   ![Diagnostik för driftsättning](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>Om du får långa fördröjningar när du försöker använda en webbtjänst som skapats med mrsdeploy-funktioner i en Spark-beräkningskontext kan du behöva lägga till vissa mappar som saknas. Spark-programmet tillhör en användare som kallas *rserve2* när den anropas från en webbtjänst med hjälp av mrsdeploy-funktioner. Så här kan du lösa problemet:

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


I det här skedet är konfigurationen för driftsättning klar. Nu kan du använda mrsdeploy-paketet på R Client för att ansluta till driftsättningen på gränsnoden. Därefter kan du använda funktionerna, till exempel [fjärrkörning](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) och [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Beroende på om klustret är konfigurerat i ett virtuellt nätverk eller inte kan du behöva konfigurera portvidarebefordran via SSH-inloggning.

### <a name="r-server-cluster-on-a-virtual-network"></a>R Server-kluster i ett virtuellt nätverk

Se till att du tillåter trafik genom port 12800 till gränsnoden. På så sätt kan du använda gränsnoden för att ansluta till driftsättningsfunktionen.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Om `remoteLogin()` inte kan ansluta till gränsnoden, men du kan använda SSH för att ansluta till gränsnoden kontrollerar du att regeln för att tillåta trafik via port 12800 har ställts in på rätt sätt. Om problemet kvarstår kan du kringgå det genom att ställa in portvidarebefordran via SSH. Mer information finns i nästa avsnitt.

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>Inget R Server-kluster installerat i ett virtuellt nätverk

Om inget kluster har konfigurerats på ett virtuellt nätverk, eller om du har problem med anslutningen via ett virtuellt nätverk, kan du använda SSH-portvidarebefordran:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Du kan också konfigurera det på PuTTY:

![PuTTY SSH-anslutning](./media/r-server-get-started/putty.png)

När din SSH-session är aktiv vidarebefordras trafiken från port 12800 i datorn till port 12800 på gränsnoden via SSH-sessionen. Se till att du använder `127.0.0.1:12800` i din `remoteLogin()`-metod. Du loggas då in på gränsnodens driftsättning via portvidarebefordran.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Skala beräkningsnoder för Microsoft R Server-driftsättning på HDInsight-arbetsnoder

### <a name="decommission-the-worker-nodes"></a>Inaktivera arbetsnoder

Microsoft R Server hanteras för närvarande inte via Yarn. Om arbetarnoderna inte är inaktiverade fungerar inte Yarn-resurshanteraren som förväntat eftersom den inte känner till resurserna som tas upp av servern. För att undvika detta rekommenderar vi att du inaktiverar arbetsnoderna innan du skalar ut beräkningsnoderna.

Så här inaktiverar du arbetsnoder:

1. Logga in på HDI-klustrets Ambari-konsol och välj fliken **Värdar**.
2. Markera arbetsnoder som ska inaktiveras och välj **Åtgärder** > **Selected Hosts** (Valda värda) > **Värdar** > **Turn On Maintenance Mode** (Aktivera underhållsläge). I bilden nedan har vi till exempel valt att inaktivera wn3 och wn4.  

   ![Skärmbild av kommandona för att aktivera underhållsläget](./media/r-server-get-started/get-started-operationalization.png)  

3. Välj **Åtgärder** > **Selected Hosts (Valda värdar)** > **DataNodes** > **Inaktivera**.
4. Välj **Åtgärder** > **Selected Hosts (Valda värdar)** > **NodeManagers** > **Inaktivera**.
5. Välj **Åtgärder** > **Selected Hosts (Valda värdar)** > **DataNodes** > **Stop** (Stoppa).
6. Välj **Åtgärder** > **Selected Hosts (Valda värdar)** > **DataNodes** > **Stop** (Stoppa).
7. Välj **Åtgärder** > **Selected Hosts (Valda värdar)** > **Värdar** > **Stop All Components (Stoppa alla komponenter)**.
8. Avmarkera arbetsnoderna och markera huvudnoderna.
9. Välj **Åtgärder** > **Selected Hosts** (Valda värdar) > **Värdar** > **Restart All Components** (Starta om alla komponenter).

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>Konfigurera beräkningsnoder på varje inaktiverad arbetsnod

1. Använd SSH för att ansluta till varje inaktiverad arbetsnod.
2. Kör ett admin-verktyg med `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Ange `1` för att välja alternativet `Configure R Server for Operationalization`.
4. Ange `c` för att välja alternativet `C. Compute node`. Då konfigureras beräkningsnoden på arbetsnoden.
5. Avsluta admin-verktyget.

### <a name="add-compute-nodes-details-on-the-web-node"></a>Lägga till beräkningsnoder på webbnod

När alla inaktiverade arbetsnoder har konfigurerats för att köra beräkningsnoder återgår du till gränsnoden och lägger till inaktiverade arbetsnoders IP-adresser i Microsoft R Server-webbnodens konfiguration:

1. Använd SSH för att ansluta till gränsnoden.
2. Kör `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
3. Leta efter avsnittet `URIs` och lägg till arbetsnodens IP och portinformation.

    ![Kommandorad för gränsnoden](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Nästa steg

Nu bör du förstå hur du skapar ett HDInsight-kluster som innehåller R Server. Du bör också förstå grunderna för att använda R-konsolen från en SSH-session. I följande artiklar beskrivs andra sätt att hantera och arbeta med R Server i HDInsight:

* [Alternativ för beräkningskontexter för R Server på HDInsight](r-server-compute-contexts.md)
* [Alternativ för Azure Storage för R Server på HDInsight](r-server-storage.md)
