---
title: Skicka jobb från R Tools för Visual Studio - Azure HDInsight
description: Skicka R-jobb från din lokala Visual Studio-dator till ett HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 73d1478ec2d6c90428f22a30ec82634df115d2f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435259"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Skicka jobb från R Tools för Visual Studio

[R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) är ett kostnadsfritt tillägg med öppen källkod för community-utgåvor (kostnadsfria), professionella och företagsutgåvor av både Visual Studio [2017](https://www.visualstudio.com/downloads/)och [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) eller senare. RTVS är inte tillgängligt för [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

RTVS förbättrar ditt R-arbetsflöde genom att erbjuda verktyg som [R Interactive-fönstret](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (kodkomplettering), [rita visualisering](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) genom R-bibliotek som ggplot2 och ggviz, [R-kodfelsökning](https://docs.microsoft.com/visualstudio/rtvs/debugging)med mera.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

1. Installera [R-verktyg för Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Installera RTVS i Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Välj arbetsbelastningen *Data science och analytiska program* och välj sedan **R-språkstöd,** **körningsstöd för R-utveckling**och **Microsoft R-klientalternativ.**

3. Du måste ha offentliga och privata nycklar för SSH-autentisering.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installera [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) på datorn. ML Server [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) tillhandahåller `RxSpark` funktionerna och.

5. Installera [PuTTY](https://www.putty.org/) för att tillhandahålla `RevoScaleR` en beräkningskontext för att köra funktioner från din lokala klient till ditt HDInsight-kluster.

6. Du har möjlighet att använda inställningarna för datavetenskap i visual studio-miljön, som ger en ny layout för arbetsytan för R-verktygen.
   1. Om du vill spara de aktuella Visual Studio-inställningarna använder du kommandot **Verktyg > importera och exportera inställningar,** sedan exportera valda **miljöinställningar** och ange ett filnamn. Om du vill återställa dessa inställningar använder du samma kommando och väljer **Importera valda miljöinställningar**.

   2. Gå till menyalternativet **R Tools** och välj sedan Inställningar **för datavetenskap...**.

       ![Inställningar för Data science i Visual Studio](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Med metoden i steg 1 kan du också spara och återställa din anpassade datavetenskapslayout i stället för att upprepa kommandot **Data Science Settings.**

## <a name="execute-local-r-methods"></a>Kör lokala R-metoder

1. Skapa ditt HDInsight ML Services-kluster.
2. Installera [RTVS-tillägget](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Ladda ner [exempel zip-fil](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Öppen `examples/Examples.sln` för att starta lösningen i Visual Studio.
5. Öppna `1-Getting Started with R.R` filen i `A first look at R` lösningsmappen.
6. Tryck på Ctrl+Retur högst upp i filen för att skicka varje rad, en i taget, till fönstret R Interactive. Vissa rader kan ta ett tag när de installerar paket.
    * Du kan också markera alla rader i R-filen (Ctrl+A), sedan antingen köra alla (Ctrl+Retur) eller markera ikonen Kör interaktiv i verktygsfältet.

        ![Visual Studio kör interaktivt](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. När du har kört alla rader i skriptet bör du se en utdata som liknar den här:

    ![Verktyg för Visual Studio-arbetsyta R](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Skicka jobb till ett HDInsight ML Services-kluster

Med hjälp av en Microsoft ML Server/Microsoft R-klient från en Windows-dator utrustad `RevoScaleR` med PuTTY kan du skapa en beräkningskontext som kör distribuerade funktioner från din lokala klient till DITT HDInsight-kluster. Används `RxSpark` för att skapa beräkningskontexten, ange ditt användarnamn, Apache Hadoop-klustrets kantnod, SSH-växlar och så vidare.

1. Ml Services kantnodsadress på `CLUSTERNAME-ed-ssh.azurehdinsight.net` HDInsight är där `CLUSTERNAME` är namnet på ml Services-klustret.

1. Klistra in följande kod i R Interactive-fönstret i Visual Studio och ändra värdena för inställningsvariablerna så att de matchar din miljö.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![apache gnista ställa in kontexten](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Kör följande kommandon i R Interactive-fönstret:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Du bör se utdata som liknar följande:

    ![Framgångsrik rx-kommandokörning](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) en
1. Kontrollera att `rxHadoopCopy` `people.json` filen har kopierats från exempeldatamappen `/user/RevoShare/newUser` till den nyskapade mappen:

    1. Välj **Lagringskonton** på menyn HDInsight ML Services i Azure.

        ![Azure HDInsight Storage-konton](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Välj standardlagringskontot för klustret och anteckna behållar-/katalognamnet.

    3. Välj **Behållare** på menyn till vänster i fönstret för lagringskonto.

        ![Azure HDInsight-lagringsbehållare](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Välj klustrets behållarnamn, bläddra till **användarmappen** (du kanske måste klicka på *Läs in mer* längst ned i listan) och välj sedan *RevoShare*och sedan **nyaAnvändare**. Filen `people.json` ska visas i `newUser` mappen.

        ![Plats för HDInsight-kopierad filmapp](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. När du är klar med den aktuella Apache Spark-kontexten måste du stoppa den. Du kan inte köra flera kontexter samtidigt.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Nästa steg

* [Beräkna kontextalternativ för ML-tjänster på HDInsight](r-server-compute-contexts.md)
* [Att kombinera ScaleR och SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) är ett exempel på prognoser för flygförsening.
