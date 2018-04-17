---
title: Skicka jobb från R-verktyg för Visual Studio - Azure HDInsight | Microsoft Docs
description: Skicka R jobb från din lokala dator i Visual Studio till ett HDInsight-kluster.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: c6f6e691ef4b317854aef1d7397d5fb840d25ff2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Skicka jobb från R Tools för Visual Studio

[R-verktyg för Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) är ett kostnadsfritt, öppen källkod tillägg för Community (kostnadsfritt), Professional och Enterprise-versioner av båda [Visual Studio 2017](https://www.visualstudio.com/downloads/), och [Visual Studio 2015 Update 3](http://go.microsoft.com/fwlink/?LinkId=691129)eller högre.

RTVS förbättrar R arbetsflödet genom att erbjuda verktyg som den [R interaktiva fönstret](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL) intellisense (kod slutförande) [Rita visualiseringen](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) via R bibliotek, till exempel ggplot2 och ggviz, [R kod felsökning](https://docs.microsoft.com/visualstudio/rtvs/debugging), med mera.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

1. Installera [R Tools för Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Installera RTVS i Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Välj den *datavetenskap och analytiska applikationer* arbetsbelastning, välj sedan den **R språkstöd**, **Runtime-stöd för R development**, och  **Microsoft R Client** alternativ.

3. Du behöver ha offentliga och privata nycklar för SSH-autentisering.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installera [R Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) på din dator. R Server ger den [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) och `RxSpark` funktioner.

5. Installera [PuTTY](http://www.putty.org/) att ge en beräkning kontext för att köra `RevoScaleR` funktioner från din lokala klient till ditt HDInsight-kluster.

6. Du har möjlighet att använda datavetenskap inställningar för din miljö för Visual Studio, vilket ger en ny layout för din arbetsyta för R-verktyg.
    1. Om du vill spara de aktuella inställningarna för Visual Studio, Använd den **Verktyg > Importera och exportera inställningar** kommando, och välj sedan **Exportera markerade miljöinställningar** och ange ett filnamn. Använd samma kommando om du vill återställa dessa inställningar och väljer **Importera markerade miljöinställningar**.

    2. Gå till den **R verktyg** menyn, välj sedan **vetenskap inställningar...** .

        ![Inställningarna för vetenskap...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > Med metoden som i steg 1, du kan också spara och återställa forskare layout för dina personliga data istället för att upprepa den **vetenskap inställningar** kommando.

## <a name="execute-local-r-methods"></a>Kör lokal R-metoder

1. Skapa din [R Server HDInsight-kluster](r-server-get-started.md).
2. Installera den [RTVS tillägget](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Hämta den [exempel zip-filen](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Öppna `examples/Examples.sln` att starta lösningen i Visual Studio.
5. Öppna den `1-Getting Started with R.R` filen i den `A first look at R` lösningsmapp.
6. Börjar överst i filen, tryck på Ctrl + Retur skicka varje rad i taget, till fönstret R interaktiva. Vissa rader kan ta en stund som paket installeras.
    * Du kan också kan du markera alla rader i filen R (Ctrl + A), och köra alla (Ctrl + Retur) eller väljer du köra interaktiva ikonen i verktygsfältet.
        ![Köra interaktiva](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Du bör se utdata som liknar detta när du har kört alla rader i skriptet:

    ![Inställningarna för vetenskap...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-r-cluster"></a>Skicka jobb till ett kluster i HDInsight R

Med en Microsoft R Server/Microsoft R-klient från en Windows-dator med PuTTY kan du skapa en kontext för beräkning som ska köra distribuerade `RevoScaleR` funktioner från din lokala klient till ditt HDInsight-kluster. Använd `RxSpark` att skapa kontexten beräkning, ange ditt användarnamn, Hadoop-kluster kantnod, SSH-växlar och så vidare.

1. Att hitta din kantnod värdnamn, öppna din R HDInsight-kluster i Azure och välj sedan **SSH (Secure Shell)** på den översta menyn i översiktsfönstret.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Kopiera den **kant nod värdnamn** värde.

    ![Värddatornamn för gränsnod](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Klistra in följande kod i fönstret R interaktiva i Visual Studio, ändra värdena för variablerna installationen så att den matchar din miljö.

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

4. Kör följande kommandon i fönstret R interaktiva:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Du bör se utdata som liknar följande:

    ![Rx kommandot har körts](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Kontrollera att den `rxHadoopCopy` har kopierade den `people.json` filen från mappen exempel data till den nyligen skapade `/user/RevoShare/newUser` mapp:

    1. I rutan din R HDInsight-kluster i Azure, Välj **lagringskonton** i den vänstra menyn.

        ![Lagringskonton](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Välj standardkontot för lagring för klustret, och meddelande om behållaren/katalognamn.

    3. Välj **behållare** i den vänstra menyn i rutan din storage-konto.

        ![Behållare](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Välj behållare klusternamnet, bläddra till den **användare** mapp (du kan behöva klicka på *läsa in mer* längst ned i listan) och välj *RevoShare*, och sedan **ny användare**. Den `people.json` fil ska visas i den `newUser` mapp.

        ![Kopierade filen](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. När du är klar med den aktuella kontexten i Spark, måste du stoppa den. Du kan inte köra flera kontexter samtidigt.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Nästa steg

* [Alternativ för beräkningskontexter för R Server på HDInsight](r-server-compute-contexts.md)
* [Kombinera ScaleR och SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) innehåller ett exempel på flygbolag svarta fördröjning förutsägelser.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
