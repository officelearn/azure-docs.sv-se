---
title: Använda MapReduce och Curl med Apache Hadoop i HDInsight - Azure
description: Lär dig hur du kör MapReduce-jobb via fjärranslutning med Apache Hadoop på HDInsight med Curl.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: ff905f34ab63027e9708082c4690e4275220854f
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53406801"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Kör MapReduce-jobb med Apache Hadoop på HDInsight med hjälp av REST

Lär dig använda Apache Hive WebHCat REST API för att köra MapReduce-jobb på en Apache Hadoop på HDInsight-kluster. CURL används för att demonstrera hur du kan interagera med HDInsight med hjälp av rådata HTTP-begäranden för att köra MapReduce-jobb.

> [!NOTE]  
> Om du redan är bekant med Linux-baserade Hadoop-servrar, men du är nybörjare på HDInsight, finns i den [vad du behöver veta om Linux-baserade Apache Hadoop på HDInsight](../hdinsight-hadoop-linux-information.md) dokumentet.


## <a id="prereq"></a>Förhandskrav

* Ett Hadoop på HDInsight-kluster
* Windows PowerShell eller [Curl](https://curl.haxx.se/) och [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Kör ett MapReduce-jobb

> [!NOTE]  
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange HDInsight-kluster administratörens användarnamn och lösenord. Du måste använda klustrets namn som en del av den URI som används för att skicka begäranden till servern.
>
> REST API skyddas med hjälp av [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda HTTPS för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. Ange klusterinloggning som används av skripten i det här dokumentet genom att använda något av kommandona followig:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Ange klusternamnet genom att använda något av följande kommandon:

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

   * **-u**: Anger det användarnamn och lösenord som används för att autentisera begäran
   * **-G**: Anger att den här åtgärden är en GET-begäran

   I början av URI: N, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, är samma för alla förfrågningar.

4. För att skicka ett MapReduce-jobb, använder du följande kommando:

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

    Slutet av URI: N (/ mapreduce/jar) meddelar WebHCat att denna begäran startar ett MapReduce-jobb från en klass i ett jar-filen. De parametrar som används i det här kommandot är följande:

   * **-d**: `-G` inte används, så att begäran som standard POST-metoden. `-d` Anger de datavärden som skickas med begäran.
    * **User.name**: Den användare som kör kommandot
    * **JAR**: Platsen för den jar-fil som innehåller klassen för att köra
    * **klassen**: Den klass som innehåller logiken som MapReduce
    * **arg**: Argument som ska skickas till MapReduce-jobb. I det här fallet, indatafilen och den katalog som används för utdata

   Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrollera status för jobbet:

       job_1415651640909_0026

5. Om du vill kontrollera status för jobbet, använder du följande kommando:

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

    Om jobbet har slutförts, tillståndet som returnerades är `SUCCEEDED`.

   > [!NOTE]  
   > Den här Curl-begäran returnerar ett JSON-dokument med information om jobbet. Jq används för att hämta tillståndet värdet.

6. När tillståndet för jobbet har ändrats till `SUCCEEDED`, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` parametern som skickas med frågan innehåller platsen för utdatafilen. I det här exemplet platsen är `/example/curl`. Den här adressen lagrar utdata för jobbet i kluster standardlagring på `/example/curl`.

Du kan visa och hämta dessa filer med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Mer information om hur du arbetar med blobar med Azure CLI finns i den [med Azure CLI med Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) dokumentet.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)

Mer information om REST-gränssnitt som används i den här artikeln finns det [WebHCat referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
