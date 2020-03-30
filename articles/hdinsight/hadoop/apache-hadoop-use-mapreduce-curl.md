---
title: Använda MapReduce och Curl med Apache Hadoop i HDInsight - Azure
description: Lär dig hur du fjärrkörer MapReduce-jobb med Apache Hadoop på HDInsight med Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302720"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Kör MapReduce-jobb med Apache Hadoop på HDInsight med REST

Lär dig hur du använder Apache Hive WebHCat REST API för att köra MapReduce-jobb på ett Apache Hadoop-kluster i HDInsight.Learn how to use the Apache Hive WebHCat REST API to run MapReduce jobs on an Apache Hadoop on HDInsight cluster. Curl används för att visa hur du kan interagera med HDInsight med hjälp av råa HTTP-begäranden för att köra MapReduce-jobb.

> [!NOTE]  
> Om du redan är bekant med att använda Linux-baserade Hadoop-servrar, men du är ny på HDInsight, se [vad du behöver veta om Linux-baserade Apache Hadoop på HDInsight-dokument.](../hdinsight-hadoop-linux-information.md)

## <a name="prerequisites"></a>Krav

* En Apache Hadoop kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md).

Antingen:
  * Windows PowerShell eller,
  * [Curl](https://curl.haxx.se/) med [jq](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Köra ett MapReduce-jobb

> [!NOTE]  
> När du använder Curl eller annan REST-kommunikation med WebHCat måste du autentisera begäranden genom att ange användarnamn och lösenord för HDInsight-klusteradministratören. Du måste använda klusternamnet som en del av den URI som används för att skicka begäranden till servern.
>
> REST API skyddas med hjälp av [grundläggande åtkomstautentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra förfrågningar med https för att säkerställa att dina autentiseringsuppgifter skickas till servern på ett säkert sätt.

### <a name="curl"></a>Curl

1. För enkel användning, ställ in variablerna nedan. Det här exemplet baseras på en Windows-miljö, ändra efter behov för din miljö.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    De parametrar som används i det här kommandot är följande:

   * **-u:** Anger användarnamn och lösenord som används för att autentisera begäran
   * **-G**: Anger att den här åtgärden är en GET-begäran

   Början av URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, är densamma för alla begäranden.

    Du får ett svar som liknar följande JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Om du vill skicka ett MapReduce-jobb använder du följande kommando. Ändra sökvägen till **jq** efter behov.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Slutet av URI (/mapreduce/jar) talar om för WebHCat att den här begäran startar ett MapReduce-jobb från en klass i en jar-fil. De parametrar som används i det här kommandot är följande:

   * **-d:** `-G` används inte, så begäran som standard till POST-metoden. `-d`anger de datavärden som skickas med begäran.
     * **user.name**: Användaren som kör kommandot
     * **jar**: Platsen för jar-filen som innehåller klass som ska kördes
     * **klass**: Den klass som innehåller MapReduce-logiken
     * **arg**: De argument som ska skickas till MapReduce-jobbet. I det här fallet kan indatatextfilen och katalogen som används för utdata

    Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrollera jobbets status:

       job_1415651640909_0026

1. Om du vill kontrollera jobbets status använder du följande kommando. Ersätt värdet `JOBID` för med det **verkliga** värdet som returnerades i föregående steg. Ändra placeringen av **jq** efter behov.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. För enkel användning, ställ in variablerna nedan. Ersätt `CLUSTERNAME` med ditt faktiska klusternamn. Kör kommandot och ange lösenordet för klusterinloggning när du uppmanas att göra det.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Använd följande kommando för att kontrollera att du kan ansluta till hdinsight-klustret:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Du får ett svar som liknar följande JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Om du vill skicka ett MapReduce-jobb använder du följande kommando:

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

    Slutet av URI (/mapreduce/jar) talar om för WebHCat att den här begäran startar ett MapReduce-jobb från en klass i en jar-fil. De parametrar som används i det här kommandot är följande:

    * **user.name**: Användaren som kör kommandot
    * **jar**: Platsen för jar-filen som innehåller klass som ska kördes
    * **klass**: Den klass som innehåller MapReduce-logiken
    * **arg**: De argument som ska skickas till MapReduce-jobbet. I det här fallet kan indatatextfilen och katalogen som används för utdata

   Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrollera jobbets status:

       job_1415651640909_0026

1. Om du vill kontrollera jobbets status använder du följande kommando:

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

### <a name="both-methods"></a>Båda metoderna

1. Om jobbet är klart är `SUCCEEDED`tillståndet som returneras .

1. När jobbets tillstånd har `SUCCEEDED`ändrats till kan du hämta resultatet av jobbet från Azure Blob storage. Parametern `statusdir` som skickas med frågan innehåller platsen för utdatafilen. I det här exemplet `/example/curl`är platsen . Den här adressen lagrar utdata för jobbet `/example/curl`i klusterstandardlagringen på .

Du kan lista och hämta dessa filer med hjälp av [Azure CLI](/cli/azure/install-azure-cli). Mer information om hur du använder Azure CLI för att arbeta med Azure Blob-lagring finns i [Snabbstart: Skapa, hämta och lista blobbar med Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Nästa steg

Mer information om andra sätt kan du arbeta med Hadoop på HDInsight:

* [Använd MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
* [Använd Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

Mer information om REST-gränssnittet som används i den här artikeln finns i [WebHCat-referensen](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
