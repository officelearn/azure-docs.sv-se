---
title: "Använd MapReduce och Curl med Hadoop i HDInsight - Azure | Microsoft Docs"
description: "Lär dig hur du Fjärrkör MapReduce-jobb med Hadoop i HDInsight med Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: e48e9f833db86f01d944133c8a32d2c6b27b7b48
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Kör jobb för MapReduce med Hadoop i HDInsight med hjälp av REST

Lär dig hur du använder WebHCat REST API för att köra MapReduce-jobb på en Hadoop på HDInsight-kluster. CURL används för att demonstrera hur du kan interagera med HDInsight med hjälp av rådata HTTP-begäranden för att köra MapReduce-jobb.

> [!NOTE]
> Om du redan är bekant med Linux-baserade Hadoop-servrar, men du är nybörjare på HDInsight, finns det [vad du behöver veta om Linux-baserade Hadoop på HDInsight](../hdinsight-hadoop-linux-information.md) dokumentet.


## <a id="prereq"></a>Förhandskrav

* En Hadoop på HDInsight-kluster
* Windows PowerShell eller [Curl](http://curl.haxx.se/) och [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Kör ett MapReduce-jobb

> [!NOTE]
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange administratörsanvändarnamn för HDInsight-kluster och lösenord. Du måste använda klustrets namn som en del av den URI som används för att skicka begäranden till servern.
>
> REST API skyddas med hjälp av [grundläggande autentisering](http://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda HTTPS för att se till att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. Om du vill ange klusterinloggning som används av skript i det här dokumentet använder du något av kommandona followig:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Om du vill ange klusternamnet, använder du någon av följande kommandon:

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

   * **-u**: anger användarnamn och lösenord som används för att autentisera begäran
   * **-G**: Anger att den här åtgärden är en GET-begäran

   I början av URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, är samma för alla begäranden.

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

    I slutet av URI: N (/ mapreduce/jar) anger WebHCat att denna begäran startar ett MapReduce-jobb från en klass i jar-filen. De parametrar som används i det här kommandot är följande:

   * **-d**: `-G` används inte, så begäran som standard POST-metoden. `-d` Anger datavärdena som skickas med begäran.
    * **User.name**: användare som kör kommandot
    * **JAR**: platsen för jar-filen som innehåller klassen kördes
    * **klassen**: klass som innehåller logiken som MapReduce
    * **%d{arg/**: argument som ska skickas till MapReduce-jobb. I det här fallet, inkommande textfilen och katalogen som används för utdata

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

    Om jobbet är klart, tillståndet returneras är `SUCCEEDED`.

   > [!NOTE]
   > Den här Curl-begäran returnerar ett JSON-dokument med information om jobbet. Jq används för att hämta tillståndet värdet.

6. När status för jobbet har ändrats till `SUCCEEDED`, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` parameter som skickas med frågan innehåller platsen för utdatafilen. I det här exemplet platsen är `/example/curl`. Den här adressen lagrar resultatet av jobbet i kluster standardlagring på `/example/curl`.

Du kan visa och ladda ned dessa filer med hjälp av den [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Mer information om hur du arbetar med blobbar från Azure CLI finns i [använder Azure CLI 2.0 med Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) dokumentet.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

Mer information om REST-gränssnitt som används i den här artikeln finns det [WebHCat referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).