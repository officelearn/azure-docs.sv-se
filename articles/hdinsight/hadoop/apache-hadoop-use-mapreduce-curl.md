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
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: dd3e5904ee21ee74da5adaa65abd7865a82c8b36
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Kör jobb för MapReduce med Hadoop i HDInsight med hjälp av REST

Lär dig hur du använder WebHCat REST API för att köra MapReduce-jobb på en Hadoop på HDInsight-kluster. CURL används för att demonstrera hur du kan interagera med HDInsight med hjälp av rådata HTTP-begäranden för att köra MapReduce-jobb.

> [!NOTE]
> Om du redan är bekant med Linux-baserade Hadoop-servrar, men du är nybörjare på HDInsight, finns det [vad du behöver veta om Linux-baserade Hadoop på HDInsight](../hdinsight-hadoop-linux-information.md) dokumentet.


## <a id="prereq"></a>Förhandskrav

* En Hadoop på HDInsight-kluster
* [CURL](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Kör MapReduce-jobb med Curl

> [!NOTE]
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange administratörsanvändarnamn för HDInsight-kluster och lösenord. Du måste använda klustrets namn som en del av den URI som används för att skicka begäranden till servern.
>
> Kommandona i det här avsnittet, ersätter **admin** med användaren för att autentisera för klustret. Ersätt **CLUSTERNAME** med namnet på klustret. När du uppmanas, ange lösenordet för användarkontot.
>
> REST API skyddas med hjälp av [grundläggande autentisering](http://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda HTTPS för att se till att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.


1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Du får ett svar som liknar följande JSON:

        {"status":"ok","version":"v1"}

    De parametrar som används i det här kommandot är följande:

   * **-u**: anger användarnamn och lösenord som används för att autentisera begäran
   * **-G**: Anger att den här åtgärden är en GET-begäran

   I början av URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, är samma för alla begäranden.

2. För att skicka ett MapReduce-jobb, använder du följande kommando:

    ```bash
    curl -u admin -d user.name=admin -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    I slutet av URI: N (/ mapreduce/jar) anger WebHCat att denna begäran startar ett MapReduce-jobb från en klass i jar-filen. De parametrar som används i det här kommandot är följande:

   * **-d**: `-G` används inte, så begäran som standard POST-metoden. `-d`Anger datavärdena som skickas med begäran.
    * **User.name**: användare som kör kommandot
    * **JAR**: platsen för jar-filen som innehåller klassen kördes
    * **klassen**: klass som innehåller logiken som MapReduce
    * **%d{arg/**: argument som ska skickas till MapReduce-jobb. I det här fallet, inkommande textfilen och katalogen som används för utdata

   Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrollera status för jobbet:

       {"id":"job_1415651640909_0026"}

3. Om du vill kontrollera status för jobbet, använder du följande kommando:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Ersätt den **JOBID** med värdet som returneras i föregående steg. Om exempelvis returvärde `{"id":"job_1415651640909_0026"}`, jobb-ID skulle vara `job_1415651640909_0026`.

    Om jobbet är klart, tillståndet returneras är `SUCCEEDED`.

   > [!NOTE]
   > Den här Curl-begäran returnerar ett JSON-dokument med information om jobbet. Jq används för att hämta tillståndet värdet.

4. När status för jobbet har ändrats till `SUCCEEDED`, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` parameter som skickas med frågan innehåller platsen för utdatafilen. I det här exemplet platsen är `/example/curl`. Den här adressen lagrar resultatet av jobbet i kluster standardlagring på `/example/curl`.

Du kan visa och ladda ned dessa filer med hjälp av den [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Mer information om hur du arbetar med blobbar från Azure CLI finns i [använder Azure CLI 2.0 med Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) dokumentet.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

Mer information om REST-gränssnitt som används i den här artikeln finns det [WebHCat referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).