---
title: Skicka jobb från R Tools för Visual Studio - Azure HDInsight
description: Skicka R-jobb från din lokala Visual Studio-dator till ett HDInsight-kluster.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 8f1ed582b7abf43afd38ca5c358aa7e179bfecb3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702280"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Skicka jobb från R Tools för Visual Studio

[R Tools för Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) är en kostnadsfri, open source-tillägg för Community (kostnadsfritt), Professional och Enterprise-versionerna av båda [Visual Studio 2017](https://www.visualstudio.com/downloads/), och [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)eller högre.

RTVS förbättrar R arbetsflödet genom att erbjuda verktyg som de [R interaktiva fönstret](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (kodifyllning), [Rita visualisering](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) via R-bibliotek, till exempel ggplot2 och ggviz, [Felsökning av R-kod](https://docs.microsoft.com/visualstudio/rtvs/debugging), med mera.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

1. Installera [R Tools för Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Installera RTVS i Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Välj den *datavetenskap och analytiska program* arbetsbelastning, välj sedan den **R språkstöd**, **Runtime-stöd för R-utveckling**, och  **Microsoft R Client** alternativ.

3. Du måste ha offentliga och privata nycklar för SSH-autentisering.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installera [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) på din dator. ML-servern tillhandahåller de [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) och `RxSpark` funktioner.

5. Installera [PuTTY](https://www.putty.org/) att tillhandahålla en beräkningskontext för att köra `RevoScaleR` funktioner från din lokala klient till ditt HDInsight-kluster.

6. Du har möjlighet att tillämpa Data Science-inställningar på din Visual Studio-miljön, vilket ger en ny layout för din arbetsyta för R-verktyg.
   1. Spara de aktuella inställningarna för Visual Studio genom att använda den **Verktyg > Importera och exportera inställningar** kommandot och välj sedan **Exportera markerade miljöinställningar** och ange ett filnamn. Om du vill återställa dessa inställningar, använder du samma kommando och välj **Importera markerade miljöinställningar**.

   2. Gå till den **R Tools** menyn, välj sedan **Data Science-inställningar...** .

       ![Data Science-inställningar...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Med metoden som i steg 1 kan du också spara och återställa dina personliga data Science layout, istället för att upprepa den **Data Science inställningar** kommando.

## <a name="execute-local-r-methods"></a>Köra lokala R-metoder

1. Skapa din [ML-tjänster för HDInsight-kluster](r-server-get-started.md).
2. Installera den [RTVS tillägget](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Ladda ned den [exempel zip-fil](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Öppna `examples/Examples.sln` att starta lösningen i Visual Studio.
5. Öppna den `1-Getting Started with R.R` fil i den `A first look at R` lösningsmappen.
6. Börjar överst i filen, tryck på Ctrl + Retur skicka varje rad, en i taget, till R interaktiva fönstret. Vissa rader kan ta en stund eftersom de installerar paket.
    * Du kan också kan du välja alla rader i R-filen (Ctrl + A), sedan antingen köra alla (Ctrl + Retur) eller väljer du köra interaktiva ikonen i verktygsfältet.
        ![Köra interaktiva](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Du bör se utdata som liknar detta när du har kört alla rader i skriptet:

    ![Data Science-inställningar...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Skicka jobb till en ML-tjänster för HDInsight-kluster

Med en Microsoft ML Server/Microsoft R Client från en Windows-dator med PuTTY kan du skapa en beräkningskontext som ska köra distribuerade `RevoScaleR` funktioner från din lokala klient till ditt HDInsight-kluster. Använd `RxSpark` att skapa beräkningskontext, ange ditt användarnamn, Apache Hadoop-klustrets kantnod, SSH växlar och så vidare.

1. För att hitta värdnamnet för din edge-nod, öppna fönstret kluster HDInsight ML-tjänster på Azure och välj sedan **Secure Shell (SSH)** på menyn överst i fönstret Översikt.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Kopiera den **värddatornamn för Gränsnod** värde.

    ![Värddatornamn för gränsnod](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Klistra in följande kod i det interaktiva R fönstret i Visual Studio, ändra värdena för variablerna installationen så att den matchar din miljö.

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
    
    ![Ange Spark-kontexten](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. Kör följande kommandon i R interaktiva fönstret:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Du bör se utdata som liknar följande:

    ![Lyckad rx Kommandokörning](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Kontrollera att den `rxHadoopCopy` kunde kopieras den `people.json` filen från mappen exempel data till den nyligen skapade `/user/RevoShare/newUser` mapp:

    1. Från fönstret din ML-tjänster för HDInsight-kluster i Azure, väljer **lagringskonton** på den vänstra menyn.

        ![Lagringskonton](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Välj standardkontot för lagring för klustret, och meddelande om behållare/katalognamnet.

    3. Välj **behållare** menyn till vänster i fönstret ditt storage-konto.

        ![Containrar](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Välj behållaren klusternamnet, bläddra till den **användaren** mapp (du kan behöva klicka på *Läs in fler* längst ned i listan) och välj sedan *RevoShare*, och sedan **ny användare**. Den `people.json` fil som ska visas i den `newUser` mapp.

        ![Kopierade filen](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Du måste stoppa när du är klar med den aktuella Apache Spark-kontexten. Du kan inte köra flera sammanhang på samma gång.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Nästa steg

* [Alternativ för beräkningskontexter för ML-tjänster på HDInsight](r-server-compute-contexts.md)
* [Kombinera ScaleR och SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) innehåller ett exempel på flygbolag flyg.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
