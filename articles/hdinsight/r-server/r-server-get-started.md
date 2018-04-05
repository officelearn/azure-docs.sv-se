---
title: Kom igång med R Server i HDInsight – Azure | Microsoft Docs
description: Lär dig att skapa en Apache Spark i ett HDInsight-kluster som innehåller R Server och sedan skicka ett R-skript i klustret.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: f4265ce7370542d8253222a5e268ea9cde7fd36e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-r-server-cluster-on-azure-hdinsight"></a>Kom igång med R Server-kluster på Azure HDInsight

I Azure HDInsight finns ett R Server-alternativ som ska integreras i HDInsight-klustret. Med det här alternativet kan R-skript använda Spark och MapReduce till att köra distribuerade beräkningar. I den här artikeln får du lära dig hur du skapar en R Server i ett HDInsight-kluster. Därefter lär du dig att köra ett R-skript som demonstrerar hur du använder Spark för distribuerade R-beräkningar.


## <a name="prerequisites"></a>Nödvändiga komponenter

* **En Azure-prenumeration**: Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Mer information finns i [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Få en kostnadsfri utvärderingsversion av Azure).
* **En SSH-klient (Secure Shell)**: En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Skapa klustret med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Klicka på **Skapa en resurs** > **Data och analys** > **HDInsight**.

3. Från **Grundläggande**, ange följande information:

    * **Klusternamn**: Namnet på HDInsight-klustret.
    * **Prenumeration**: Välj den prenumeration som du vill använda.
    * **Användarnamn för klusterinloggning** och **Lösenord för klusterinloggning**: Inloggningen vid åtkomst till klustret via HTTPS. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till tjänster som Ambari-webbgränssnittet eller REST API.
    * **Secure Shell-användarnamn (SSH)**: Den inloggning som används vid åtkomst till klustret via SSH. Som standard är lösenordet detsamma som lösenordet för klusterinloggning.
    * **Resursgrupp**: Resursgruppen som klustret ska skapas i.
    * **Plats**: Azure-region som klustret ska skapas i.

        ![Grundläggande information om klustret](./media/r-server-get-started/clustername.png)

4. Välj **Klustertyp** och ange följande värden i avsnittet **Klusterkonfiguration**:

    * **Klustertyp**: R Server

    * **Operativsystem**: Linux

    * **Version**: R Server 9.1 (HDI 3.6). Viktig information för tillgängliga versioner av R Server finns på [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).

    * **R Studio community edition för R Server**: Denna webbläsarbaserade IDE installeras som standard på gränsnoden. Om du föredrar att inte installera den, avmarkerar du kryssrutan. Om du väljer att installera den, finns URL:en till RStudio Server-inloggningen på portalprogrambladet för ditt kluster när det har skapats.

        ![Grundläggande information om klustret](./media/r-server-get-started/clustertypeconfig.png)

4. När du har valt klustertypen anger du klustertypen med hjälp av knappen __Välj__. Använd sedan knappen __Nästa__ och slutföra den grundläggande konfigurationen.

5. Gå till avsnittet **Lagring** och välj eller skapa ett lagringskonto. Lämna övriga fält i det här avsnittet på standardvärden för anvisningarna i det här dokumentet. Spara lagringskonfigurationen genom att klicka på __Nästa__.

    ![Ange inställningarna för lagringskontot för HDInsight](./media/r-server-get-started/cluster-storage.png)

6. Gå till avsnittet **Sammanfattning** och granska konfigurationen för klustret. Använd länkarna __Redigera__ om du behöver ändra eventuella inställningar som är felaktiga. Till sist skapar du klustret genom att klicka på __Skapa__.

    ![Ange inställningarna för lagringskontot för HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > Det kan ta upp till 20 minuter att skapa klustret.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Anslut till RStudio Server

Om du väljer att installera RStudio Server Community Edition som en del av ditt HDInsight-kluster, kan du komma åt RStudio-inloggningen med en av följande två metoder:

* **Alternativ 1** – Gå till följande URL (där **KLUSTERNAMN** är namnet på det R Server-kluster som du skapade):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Alternativ 2** – Öppna R Server-klustret i Azure Portal, under **Snabblänkar** klickar du på **R Server-instrumentpaneler**.

     ![Ange inställningarna för lagringskontot för HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    Från **Klusterinstrumentpaneler**, klickar du på **R Studio Server**.

    ![Ange inställningarna för lagringskontot för HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Oavsett vilken metod du väljer måste du autentisera dig två gånger när du loggar in för första gången.  I den första autentiseringsprompten anger du *klusteradministratörens användar-id* och *lösenord*. I den andra autentiseringsprompten anger du *SSH användar-id* och *lösenord*. Efterföljande inloggningar kräver endast SSH-autentiseringsuppgifter.

När du är ansluten, bör din skärm likna följande skärmbild:

![Anslut till RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Kör ett exempeljobb

Du kan skicka in ett jobb med hjälp av ScaleR-funktioner. Här är ett exempel på kommandon som används till att köra ett jobb:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
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

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Anslut till en klustergränsnoden

I det här avsnittet får du lära dig att ansluta till gränsnoden för ett R Server HDInsight-kluster med SSH. Om du behöver bekanta dig med SSH, se [Använd SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

SSH-kommandot för att ansluta till R Server-klustergränsnoden är:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Om du vill hitta SSH-kommandot för ditt kluster i Azure Portal, klickar du på klustrets namn, klickar på **SSH- + klusterinloggningen** och sedan som **Värddatornamn** väljer du gränsnoden. Då visas SSH-slutpunktsinformation för kantnoden.

![Avbildning av SSH-slutpunkten för kantnoden](./media/r-server-get-started/sshendpoint.png)

Om du skyddat SSH-användarkontot med lösenord uppmanas du att ange det. Om du använde en offentlig nyckel kan du behöva använda `-i`-parametern för att ange motsvarande privata nyckel. Till exempel:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

När du är ansluten får du en uppmaning som liknar följande:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-server-console"></a>Använd R Server-konsolen

1. Starta R-konsolen från SSH-sessionen med följande kommando:  

        R

2. Du bör se utdata med versionen av R-Server, samt annan information.
    
3. Från frågan `>` kan du ange R-kod. R Server på HDInsight innehåller paket som gör att du enkelt kan interagera med Hadoop och köra distribuerade beräkningar. Du kan exempelvis använda följande kommando till att visa roten för standardfilsystemet för HDInsight-klustret:

        rxHadoopListFiles("/")

4. Du kan också använda adressering i WASB-format.

        rxHadoopListFiles("wasb:///")

5. Om du vill avsluta R-konsolen, använder du följande kommando:

        quit()

## <a name="automated-cluster-creation"></a>Skapa kluster automatiskt

Du kan automatisera skapandet av R Server-kluster för HDInsight med hjälp av Azure Resource Manager-mallar, SDK och PowerShell.

* Om du vill skapa ett R Server-kluster med en Azure Resource Manager-mall, se [Distribuera en R Server för HDInsight-kluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
* Om du vill skapa ett R Server-kluster med .NET SDK, se [Skapa Linux-baserade kluster i HDInsight med .NET SDK.](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* Om du vill skapa ett R Server-kluster med PowerShell, se artikeln [Skapa HDInsight-kluster med Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du läsa om hur man skapar ett nytt R Server-kluster i Azure HDInsight och grunderna i hur man använder R-konsolen från en SSH-session. Följande artiklar beskriver andra sätt att hantera och arbeta med R Server i HDInsight:

* [Skicka in jobb från R-verktyg för Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Hantera R Server-kluster i HDInsight](r-server-hdinsight-manage.md)
* [Operationalisera R Server-kluster i HDInsight](r-server-operationalize.md)
* [Alternativ för beräkningskontexter för R Server-kluster i HDInsight](r-server-compute-contexts.md)
* [Alternativ för Azure Storage för R Server-kluster i HDInsight](r-server-storage.md)
