---
title: Använda MapReduce och sväng med Apache Hadoop i HDInsight – Azure
description: Lär dig hur du fjärrköra MapReduce-jobb med Apache Hadoop i HDInsight med hjälp av sväng.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/27/2018
ms.openlocfilehash: 274d8dc80d9318aa3ddf4a904a5b623319ea01f4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645012"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Kör MapReduce-jobb med Apache Hadoop på HDInsight med REST

Lär dig hur du använder Apache Hive WebHCat-REST API för att köra MapReduce-jobb på en Apache Hadoop på HDInsight-kluster. Sväng används för att demonstrera hur du kan interagera med HDInsight genom att använda RAW HTTP-begäranden för att köra MapReduce-jobb.

> [!NOTE]  
> Om du redan är bekant med att använda Linux-baserade Hadoop-servrar, men du är nybörjare på HDInsight, kan du läsa [vad du behöver veta om Linux-baserade Apache Hadoop på HDInsight-](../hdinsight-hadoop-linux-information.md) dokument.


## <a id="prereq"></a>Förhandskrav

* Ett Hadoop på HDInsight-kluster
* Windows PowerShell eller [sväng](https://curl.haxx.se/) och [JQ](https://stedolan.github.io/jq/)

## <a id="curl"></a>Kör ett MapReduce-jobb

> [!NOTE]  
> När du använder en sväng eller någon annan REST-kommunikation med WebHCat måste du autentisera begär Anden genom att ange användar namnet och lösen ordet för HDInsight-klustret. Du måste använda kluster namnet som en del av den URI som används för att skicka begär anden till servern.
>
> REST API skyddas med hjälp av [grundläggande åtkomst-autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begär Anden genom att använda HTTPS för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. Använd något av följande kommandon för att ange den kluster inloggning som används av skripten i det här dokumentet:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Ange kluster namnet med något av följande kommandon:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Du får ett svar som liknar följande JSON:

        {"status":"ok","version":"v1"}

    De parametrar som används i det här kommandot är följande:

   * **-u**: anger det användar namn och lösen ord som används för att autentisera begäran
   * **-G**: visar att den här åtgärden är en get-begäran

   Början av URI: n `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, är samma för alla begär Anden.

4. Om du vill skicka ett MapReduce-jobb använder du följande kommando:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Slutet av URI: n (/MapReduce/jar) anger WebHCat att den här begäran startar ett MapReduce-jobb från en klass i en jar-fil. De parametrar som används i det här kommandot är följande:

   * **-d**: `-G` används inte, så standardvärdet för begäran till post-metoden. `-d` anger de data värden som skickas med begäran.
     * **User.name**: användaren som kör kommandot
     * **jar**: platsen för den jar-fil som innehåller klassen som ska köras
     * **klass**: klassen som innehåller MapReduce-logiken
     * **arg**: argumenten som ska skickas till MapReduce-jobbet. I det här fallet är indatafilen och den katalog som används för utdata

   Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrol lera jobbets status:

       job_1415651640909_0026

5. Använd följande kommando för att kontrol lera jobbets status:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Om jobbet är klart `SUCCEEDED`det tillstånd som returneras.

   > [!NOTE]  
   > Denna förfrågan returnerar ett JSON-dokument med information om jobbet. JQ används för att hämta endast State-värdet.

6. När jobbets tillstånd har ändrats till `SUCCEEDED`kan du hämta resultatet från Azure Blob Storage. Parametern `statusdir` som skickas med frågan innehåller platsen för utdatafilen. I det här exemplet är platsen `/example/curl`. Den här adressen lagrar utdata från jobbet i kluster standard lagringen på `/example/curl`.

Du kan visa och hämta dessa filer med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Mer information om hur du arbetar med blobbar från Azure CLI finns i [använda Azure CLI med Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) -dokument.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
* [Använda Apache gris med Apache Hadoop på HDInsight](hdinsight-use-pig.md)

Mer information om REST-gränssnittet som används i den här artikeln finns i referens för [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
