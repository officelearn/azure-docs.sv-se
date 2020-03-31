---
title: Hantera ML Services-kluster på HDInsight - Azure
description: Lär dig hur du hanterar olika uppgifter i ML Services-klustret i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: b2c16c27c0dfc0c30a99c52544cc4d2278eadfc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647738"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Hantera ML Services-kluster på Azure HDInsight

I den här artikeln får du lära dig hur du hanterar ett befintligt ML-tjänstkluster på Azure HDInsight för att utföra uppgifter som att lägga till flera samtidiga användare, fjärranslutning till ett ML Services-kluster, ändra beräkningskontext osv.

## <a name="prerequisites"></a>Krav

* Ett ML Services-kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **ML Services** för **klustertyp**.

* En SSH-klient (Secure Shell): En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [Använda SSH med HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="enable-multiple-concurrent-users"></a>Aktivera flera samtidiga användare

Du kan aktivera flera samtidiga användare för ML Services-klustret på HDInsight genom att lägga till fler användare för kantnoden som RStudio-communityversionen körs på. När du skapar ett HDInsight-kluster måste du ange två användare, en HTTP-användare och en SSH-användare:

![Inloggningsparametrar för HDI Azure-portalen](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- **Användarnamn för klusterinloggning**: en HTTP-användare för autentisering via HDInsight-gatewayen som används till att skydda HDInsight-klustret du skapade. Denna HTTP-användare används för att komma åt Apache Ambari UI, Apache Hadoop YARN UI, liksom andra gränssnitt komponenter.
- **Secure Shell (SSH)-användarnamn**: en SSH-användare för åtkomst till klustret via SSH. Den här användaren är en användare i Linux-systemet för alla huvudnoder, arbetsnoder och kantnoder. Du kan därmed använda SSH för åtkomst till alla noder i ett fjärrkluster.

R Studio Server Community-versionen som används i ML Services-klustret på HDInsight accepterar endast Användarnamn och lösenord för Linux som inloggningsmekanism. Du kan inte skicka token. Så när du försöker komma åt R Studio för första gången i ett ML Services-kluster måste du logga in två gånger.

- Logga först in med HTTP-användarautentiseringsuppgifterna via HDInsight Gateway.

- Använd sedan SSH-användarautentiseringsuppgifterna för att logga in på RStudio.
  
För närvarande kan du bara skapa ett SSH-användarkonto när du etablerar ett HDInsight-kluster. Så för att göra det möjligt för flera användare att komma åt ML Services-kluster på HDInsight måste du skapa ytterligare användare i Linux-systemet.

Eftersom RStudio körs på klustrets kantnod finns det flera steg här:

1. Använda den befintliga SSH-användaren för att logga in på kantnoden
2. Lägg till fler Linux-användare på kantnoden
3. Använd RStudio Community-versionen med användaren som skapades

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Steg 1: Använd den skapade SSH-användaren för att logga in på kantnoden

Följ instruktionerna på [Connect to HDInsight (Apache Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) för att komma åt kantnoden. Edge-nodadressen för ML Services-klustret på HDInsight är `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Steg 2: Lägg till fler Linux-användare på kantnoden

Kör följande kommandon när du ska lägga till en användare vid kantnoden:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Följande skärmbild visar utgångarna.

![skärmdumputdata samtidiga användare](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

När du uppmanas att ange "Aktuellt Kerberos-lösenord:" trycker du bara på **Retur** för att ignorera det. Alternativet `-m` i kommandot `useradd` innebär att systemet skapar en arbetsmapp för användaren, vilket krävs för RStudio Comunity-versionen.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Steg 3: Använd RStudio Community-versionen med användaren som skapades

Få tillgång `https://CLUSTERNAME.azurehdinsight.net/rstudio/`till RStudio från . Om du loggar in för första gången efter att du har skapat klustret anger du autentiseringsuppgifterna för klusteradministratören följt av de SSH-användarautentiseringsuppgifter som du skapade. Om detta inte är din första inloggning anger du bara autentiseringsuppgifterna för den SSH-användare som du skapade.

Du kan också logga in med de ursprungliga autentiseringsuppgifterna (som standard är det *sshuser)* samtidigt från ett annat webbläsarfönster.

Observera också att de nya användarna inte har rotbehörighet i Linux-systemet, men att de har samma åtkomst till alla filer i HDFS- och WASB-fjärrlagringen.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Ansluta på distans till Microsoft ML Services

Du kan ställa in åtkomst till HDInsight Spark-beräkningskontexten från en fjärrinstans av ML-klient som körs på skrivbordet. För att göra detta måste du ange alternativen (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches och sshProfileScript) när du definierar RxSpark-beräkningskontexten på skrivbordet: Till exempel:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
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

Mer information finns i avsnittet "Använda Microsoft Machine Learning Server som Apache Hadoop-klient" i [Så här använder du RevoScaleR i en Apache Spark-beräkningskontext](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Använda en beräkningskontext

Med en beräkningskontext kan du kontrollera om beräkningen utförs lokalt på kantnoden eller om den ska distribueras mellan noderna i HDInsight-klustret.  Ett exempel på att ange en beräkningskontext med RStudio Server finns i [Köra ett R-skript i ett ML Services-kluster i Azure HDInsight med RStudio Server](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuera R-kod till flera noder

Med ML Services på HDInsight kan du ta befintlig R-kod och `rxExec`köra den över flera noder i klustret med hjälp av . Det här är praktiskt när du gör en parameterrensning eller simuleringar. Här följer ett kodexempel på hur du kan använda `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Om du fortfarande använder Spark-kontexten returnerar det här kommandot nodnamnet för de arbetsnoder som koden `(Sys.info()["nodename"])` körs på. På ett kluster med fyra noder förväntar du dig till exempel att ta emot utdata som liknar följande kodavsnitt:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Få tillgång till data i Apache Hive och Parkett

HDInsight ML Services ger direkt åtkomst till data i Hive och Parkett för användning av ScaleR-funktioner i Spark-beräkningskontexten. Dessa funktioner är tillgängliga via nya ScaleR-datakällafunktioner som heter RxHiveData och RxParquetData som fungerar med Spark SQL för att läsa in data direkt till en Spark DataFrame för analys av ScaleR.

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


Mer information om hur du använder dessa nya funktioner finns `?RxHivedata` i `?RxParquetData` onlinehjälpen i ML-tjänster med hjälp av kommandon och kommandon.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Installera ytterligare R-paket i klustret

### <a name="to-install-r-packages-on-the-edge-node"></a>Så här installerar du R-paket på kantnoden

Om du vill installera ytterligare R-paket på kantnoden kan du använda `install.packages()` direkt inifrån R-konsolen, när du är ansluten till kantnoden via SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Så här installerar du R-paket på arbetarnoden

Om du vill installera R-paket på arbetarnoderna i klustret måste du använda en skriptåtgärd. Skriptåtgärder är bash-skript som används till att göra konfigurationsändringar i HDInsight-klustret eller till att installera ytterligare programvara, som fler R-paket. 

> [!IMPORTANT]  
> Det går bara att använda skriptåtgärder för att installera ytterligare R-paket när klustret har skapats. Använd inte den här proceduren när klustret skapas, eftersom skriptet förlitar sig på att ML-tjänster är helt konfigurerade.

1. Följ stegen i [Anpassa kluster med hjälp av skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md).

3. För **Skicka skriptåtgärd**anger du följande information:

   * För **skripttyp**väljer du **Anpassad**.

   * För **Namn**anger du ett namn för skriptåtgärden.

     * För **Bash script URI**anger du `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Det här är skriptet som installerar ytterligare R-paket på arbetarnoden

   * Markera kryssrutan endast för **Arbetare**.

   * **Parametrar**: R-paketen som ska installeras. Till exempel, `bitops stringr arules`

   * Markera kryssrutan om du vill **bevara den här skriptåtgärden**.  

   > [!NOTE]
   > 1. Som standard installeras alla R-paket från en ögonblicksbild av Microsoft MRAN-databasen som överensstämmer med den version av ML Server som har installerats. Om du vill installera nyare version av paket finns en risk för kompatibilitetsproblem. Den här typen av installation är däremot möjlig om du anger `useCRAN` som det första elementet i paketlistan, till exempel `useCRAN bitops, stringr, arules`.  
   > 2. För vissa R-paket krävs ytterligare Linux-bibliotek. För enkelhetens skull kommer HDInsight ML Services förinstallerat med de beroenden som behövs av de 100 mest populära R-paketen. Men om R-paket som du installerar kräver bibliotek utöver dessa måste du ladda ned basskriptet som används här och lägga till steg för att installera systembiblioteken. Sedan måste du överföra de ändrade skripten till en offentlig blobcontainer i Azure Storage och använda det ändrade skriptet för att installera paketen.
   >    Mer information om hur du utvecklar skriptåtgärder finns i [Skriptåtgärdsutveckling](../hdinsight-hadoop-script-actions-linux.md).

   ![Skriptåtgärd för Skicka skript i Azure-portalen](./media/r-server-hdinsight-manage/submit-script-action.png)

4. Välj **Skapa** för att köra skriptet. När skriptet är färdigt är R-paketen tillgängliga på alla arbetsnoder.

## <a name="next-steps"></a>Nästa steg

* [Operationalisera ML-tjänstkluster i HDInsight](r-server-operationalize.md)
* [Beräkningskontextalternativ för ML-tjänstkluster på HDInsight](r-server-compute-contexts.md)
* [Alternativ för Azure Storage för ML-tjänstkluster i HDInsight](r-server-storage.md)
