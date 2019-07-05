---
title: Hantera ML-Services-kluster i HDInsight - Azure
description: Lär dig mer om att hantera ett kluster för ML-tjänster i Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: fa838f371607f3c0b0f76f81d6755c842a5901f7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448964"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Hantera kluster för ML-tjänster på Azure HDInsight

I den här artikeln får du lära dig hantera ett befintligt kluster för ML-tjänster på Azure HDInsight för att utföra uppgifter som att lägga till flera samtidiga användare, fjärransluta till ett kluster som ML-tjänster, ändra beräkningskontext osv.

## <a name="prerequisites"></a>Förutsättningar

* En ML-Services-kluster i HDInsight. Se [skapa Apache Hadoop-kluster med Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **ML tjänster** för **Klustertyp**.


* En Secure Shell (SSH)-klient: En SSH-klient används för att ansluta till HDInsight-kluster och köra kommandon direkt i klustret. Mer information finns i [använda SSH med HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Aktivera flera samtidiga användare

Du kan aktivera flera samtidiga användare för ML-Services-kluster i HDInsight genom att lägga till fler användare för kantnoden som RStudio community-versionen körs. När du skapar ett HDInsight-kluster måste du ange två användare, en HTTP-användare och en SSH-användare:

![Samtidig användare 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Användarnamn för klusterinloggning**: en HTTP-användare för autentisering via HDInsight-gatewayen som används till att skydda HDInsight-klustret du skapade. Den här HTTP-användaren används för att komma åt Apache Ambari UI, Apache Hadoop YARN UI samt andra gränssnittskomponenter.
- **Secure Shell (SSH)-användarnamn**: en SSH-användare för åtkomst till klustret via SSH. Den här användaren är en användare i Linux-systemet för alla huvudnoder, arbetsnoder och kantnoder. Du kan därmed använda SSH för åtkomst till alla noder i ett fjärrkluster.

R Studio Server Community-versionen som används i ML-Services-kluster i HDInsight accepterar endast Linux-användarnamn och lösenord som en inloggning mekanism. Du kan inte skicka token. När du försöker få åtkomst till R Studio för första gången på ett kluster för ML-tjänster behöver du logga in två gånger.

- Logga först in med HTTP-autentiseringsuppgifterna via HDInsight-gatewayen. 

- Använd sedan SSH-autentiseringsuppgifterna för att logga in på RStudio.
  
För närvarande kan du bara skapa ett SSH-användarkonto när du etablerar ett HDInsight-kluster. Så om du vill aktivera flera användare åtkomst till tjänster för ML-kluster i HDInsight, måste du skapa ytterligare användare i Linux-systemet.

Eftersom RStudio körs på klustrets kantnod ingår flera steg i detta:

1. Använd den befintliga SSH-användaren för att logga in på kantnoden
2. Lägg till fler Linux-användare på kantnoden
3. Använd RStudio Community-versionen med användaren som skapades

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Steg 1: Använd den skapade SSH-användaren för att logga in på kantnoden

Följ anvisningarna på [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) att få åtkomst till kantnoden. Gränsnodsadressen till ML-Services-kluster i HDInsight är `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Steg 2: Lägg till fler Linux-användare på kantnoden

Kör följande kommandon när du ska lägga till en användare vid kantnoden:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

I följande skärmbild visas utdata.

![Samtidig användare 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

När du tillfrågas om ”aktuella Kerberos-lösenordet”:, trycker du bara på **RETUR** att den ska ignoreras. Alternativet `-m` i kommandot `useradd` innebär att systemet skapar en arbetsmapp för användaren, vilket krävs för RStudio Comunity-versionen.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Steg 3: Använd RStudio Community-versionen med användaren som skapades

Komma åt RStudio från https://CLUSTERNAME.azurehdinsight.net/rstudio/. Om du loggar in för första gången när du har skapat klustret, anger du autentiseringsuppgifter för administratör kluster, följt av SSH-autentiseringsuppgifterna som du skapade. Om det inte är din första inloggning kan endast ange autentiseringsuppgifterna för SSH-användaren som du skapade.

Du kan också logga in med de ursprungliga autentiseringsuppgifterna (som standard är det *sshuser*) samtidigt från ett annat webbläsarfönster.

Observera också att de nya användarna inte har rotbehörighet i Linux-systemet, men att de har samma åtkomst till alla filer i HDFS- och WASB-fjärrlagringen.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Fjärransluta till Microsoft ML-tjänster

Du kan konfigurera åtkomst till HDInsight Spark-beräkningskontexten från en fjärrinstans av ML-klienten som körs på skrivbordet. Om du vill göra det måste du ange alternativ (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches och sshProfileScript) när definierar RxSpark-beräkningskontext på skrivbordet: Exempel:

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

Mer information finns i avsnittet ”med hjälp av Microsoft Machine Learning Server som en Apache Hadoop Client” i [hur du använder RevoScaleR i ett Apache Spark-beräkningskontext](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Använda en beräkningskontext

Med en beräkningskontext kan du kontrollera om beräkningen utförs lokalt på kantnoden eller om den ska distribueras mellan noderna i HDInsight-klustret.  Ett exempel på hur en beräkningskontext med RStudio Server Se [köra ett R-skript på ett kluster som ML-tjänster i Azure HDInsight med hjälp av RStudio Server](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuera R-kod till flera noder

Med ML-tjänster på HDInsight, kan du ta befintlig R-kod och köra den mot flera noder i klustret med `rxExec`. Det här är praktiskt när du gör en parameterrensning eller simuleringar. Här följer ett kodexempel på hur du kan använda `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Om du fortfarande använder Spark-kontexten, det här kommandot returnerar värdet för nodnamnet för arbetsnoderna som koden `(Sys.info()["nodename"])` körs på. Till exempel på ett kluster med fyra noder du förväntar dig att få utdata som liknar följande fragment:

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

## <a name="access-data-in-apache-hive-and-parquet"></a>Åtkomst till data i Apache Hive och Parquet

HDInsight ML-tjänster kan direkt åtkomst till data i Hive och Parquet för användning av ScaleR-funktioner i Spark-beräkningskontexten. Dessa funktioner är tillgängliga via nya ScaleR-datakällafunktioner som heter RxHiveData och RxParquetData som fungerar med Spark SQL för att läsa in data direkt till en Spark DataFrame för analys av ScaleR.

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


För ytterligare information om användning av dessa nya funktioner finns i onlinehjälpen i ML-tjänster med hjälp av den `?RxHivedata` och `?RxParquetData` kommandon.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Installera ytterligare R-paket i klustret

### <a name="to-install-r-packages-on-the-edge-node"></a>Installera R-paket på kantnoden

Om du vill installera ytterligare R-paket på kantnoden kan du använda `install.packages()` direkt inifrån R-konsolen när ansluten till gränsnoden via SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Installera R-paket på arbetsnoden

Om du vill installera R-paket på arbetsnoderna för klustret, måste du använda en skriptåtgärd. Skriptåtgärder är bash-skript som används till att göra konfigurationsändringar i HDInsight-klustret eller till att installera ytterligare programvara, som fler R-paket. 

> [!IMPORTANT]  
> Det går bara att använda skriptåtgärder för att installera ytterligare R-paket när klustret har skapats. Använd inte den här proceduren när du skapar klustret eftersom skriptet förlitar sig på ML-tjänster som konfigurerats helt.

1. Följ stegen i [anpassa kluster med skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md).

3. För **skicka skriptåtgärd**, ange följande information:

   * För **Skripttyp**väljer **anpassad**.

   * För **namn**, ange ett namn för skriptåtgärden.

     * För **Bash-skript-URI: N**, ange `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Det här är de skript som installerar ytterligare R-paket på arbetsnoden

   * Markera kryssrutan för **Worker**.

   * **Parametrar**: R-paket som ska installeras. Till exempel, `bitops stringr arules`

   * Markera kryssrutan för att **spara den här skriptåtgärden**.  

   > [!NOTE]
   > 1. Som standard installeras alla R-paket från en ögonblicksbild av Microsoft MRAN-lagringsplatsen konsekvent med versionen av ML-Server som har installerats. Om du vill installera nyare version av paket finns en risk för kompatibilitetsproblem. Den här typen av installation är däremot möjlig om du anger `useCRAN` som det första elementet i paketlistan, till exempel `useCRAN bitops, stringr, arules`.  
   > 2. För vissa R-paket krävs ytterligare Linux-bibliotek. För att underlätta för levereras ML-tjänster HDInsight förinstallerad med beroenden som krävs av de översta 100 populäraste R-paketen. Men om R-paket som du installerar kräver bibliotek utöver dessa måste du ladda ned basskriptet som används här och lägga till steg för att installera systembiblioteken. Sedan måste du överföra de ändrade skripten till en offentlig blobcontainer i Azure Storage och använda det ändrade skriptet för att installera paketen.
   >    Mer information om hur du utvecklar skriptåtgärder finns i [Skriptåtgärdsutveckling](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Lägga till en skriptåtgärd](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Välj **Skapa** för att köra skriptet. När skriptet är färdigt är R-paketen tillgängliga på alla arbetsnoder.

## <a name="next-steps"></a>Nästa steg

* [Operationalisera ML-tjänstkluster i HDInsight](r-server-operationalize.md)
* [Alternativ för beräkningskontexter för ML Service-kluster i HDInsight](r-server-compute-contexts.md)
* [Alternativ för Azure Storage för ML-tjänstkluster i HDInsight](r-server-storage.md)
