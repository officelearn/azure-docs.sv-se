---
title: "Använda Hadoop Pig med övriga i HDInsight - Azure | Microsoft Docs"
description: "Lär dig hur du använder REST för att köra Pig Latin på ett Hadoop-kluster i Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.openlocfilehash: c2c441d92607a017b4451cd8a06fb78a5e8e7135
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Köra Pig med Hadoop på HDInsight med hjälp av REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Lär dig mer om att köra Pig Latin-jobb genom att göra övriga begäranden till ett Azure HDInsight-kluster. CURL används för att demonstrera hur du kan interagera med HDInsight med WebHCat REST API.

> [!NOTE]
> Om du redan är bekant med Linux-baserade Hadoop-servrar, men nya HDInsight, se [Linux-baserade HDInsight-Tips](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Förhandskrav

* Ett Azure HDInsight (Hadoop på HDInsight)-kluster (Linux- eller Windows-baserade)

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [CURL](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Köra Pig-jobb med Curl

> [!NOTE]
> REST API skyddas via [grundläggande autentisering](http://en.wikipedia.org/wiki/Basic_access_authentication). Alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.
>
> När du använder kommandona i det här avsnittet, ersätter `USERNAME` med användaren för att autentisera för klustret och ersätter `PASSWORD` med lösenordet för användarkontot. Ersätt `CLUSTERNAME` med namnet på klustret.
>


1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Du bör få följande JSON-svar:

        {"status":"ok","version":"v1"}

    De parametrar som används i det här kommandot är följande:

    * **-u**: användarnamn och lösenord som används för att autentisera begäran
    * **-G**: Anger att begäran är en GET-begäran

     I början av URL-Adressen **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, är samma för alla begäranden. Sökvägen **/status**, anger att begäran ska returnera statusen för WebHCat (även kallat Templeton) för servern.

2. Du kan använda följande kod för att skicka ett Pig Latin-jobb till klustret:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    De parametrar som används i det här kommandot är följande:

    * **-d**: eftersom `-G` används inte begäran som standard POST-metoden. `-d`Anger datavärdena som skickas med begäran.

    * **User.name**: användare som kör kommandot
    * **köra**: den Pig Latin-instruktioner för att köra
    * **statusdir**: katalogen som status för jobbet skrivs till

    > [!NOTE]
    > Observera att blanksteg i Pig Latin rapporterna har ersatts av den `+` tecken när det används med Curl.

    Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrollera status för jobbet, till exempel:

        {"id":"job_1415651640909_0026"}

3. Använd följande kommando för att kontrollera status för jobbet

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Ersätt `JOBID` med värdet som returneras i föregående steg. Om exempelvis returvärde `{"id":"job_1415651640909_0026"}`, sedan `JOBID` är `job_1415651640909_0026`.

    Om jobbet har slutförts i tillståndet är **lyckades**.

    > [!NOTE]
    > Den här Curl-begäran returnerar ett JavaScript Object Notation (JSON) dokument med information om jobbet och jq används för att hämta tillståndet värdet.

## <a id="results"></a>Visa resultat

När status för jobbet har ändrats till **lyckades**, kan du hämta resultatet av jobbet. Den `statusdir` -parameter som överförs med frågan innehåller platsen för utdatafilen; i det här fallet `/example/pigcurl`.

HDInsight kan använda Azure Storage eller Azure Data Lake Store som standard dataarkiv. Det finns olika sätt att hämta data beroende på vilket som du använder. Mer information finns i avsnittet lagring av den [Linux-baserat HDInsight information](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) dokumentet.

## <a id="summary"></a>Sammanfattning

Som visas i det här dokumentet, kan du använda en rå HTTP-begäran att köra, övervaka och visa resultatet av Pig-jobb på ditt HDInsight-kluster.

Mer information om REST-gränssnitt som används i den här artikeln finns det [WebHCat referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Nästa steg

Allmän information om Pig på HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)
