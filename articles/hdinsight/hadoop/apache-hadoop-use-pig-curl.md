---
title: Använda Pig med Hadoop med vila i HDInsight - Azure
description: Lär dig hur du använder REST för att köra Pig Latin-jobb på ett Hadoop-kluster i Azure HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: jasonh
ms.openlocfilehash: 6804e4661948c444db0ec3ecc241abc8ba6e00eb
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599113"
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Köra Pig-jobb med Hadoop på HDInsight med hjälp av REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Lär dig hur du kör Pig Latin-jobb genom att göra REST-begäranden till ett Azure HDInsight-kluster. CURL används för att demonstrera hur du kan interagera med HDInsight med hjälp av WebHCat REST-API.

> [!NOTE]
> Om du redan är bekant med Linux-baserade Hadoop-servrar, men är nybörjare på HDInsight, se [Tips för Linux-baserade HDInsight](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Förhandskrav

* Ett Azure HDInsight (Hadoop på HDInsight)-kluster (Linux-baserade eller Windows-baserade)

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [CURL](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Köra Pig-jobb med hjälp av Curl

> [!NOTE]
> REST API skyddas via [grundläggande autentisering](http://en.wikipedia.org/wiki/Basic_access_authentication). Alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.
>
> När du använder kommandona i det här avsnittet, ersätter `USERNAME` med användaren för att autentisera för klustret och Ersätt `PASSWORD` med lösenordet för användarkontot. Ersätt `CLUSTERNAME` med namnet på klustret.
>


1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Du bör få följande JSON-svar:

        {"status":"ok","version":"v1"}

    De parametrar som används i det här kommandot är följande:

    * **-u**: användarnamn och lösenord som används för att autentisera begäran
    * **-G**: Anger att den här begäran är en GET-begäran

     I början av URL: en, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, är samma för alla förfrågningar. Sökvägen, **/status**, indikerar att förfrågan är att returnera statusen för WebHCat (även kallat Templeton) för servern.

2. Använd följande kod för att skicka ett Pig Latin-jobb i klustret:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    De parametrar som används i det här kommandot är följande:

    * **-d**: eftersom `-G` används inte begäran som standard POST-metoden. `-d` Anger de datavärden som skickas med begäran.

    * **User.name**: användare som kör kommandot
    * **köra**: The Pig Latin-uttryck för att köra
    * **statusdir**: katalogen som status för det här jobbet skrivs till

    > [!NOTE]
    > Observera att blanksteg i satser i Pig Latin har ersatts av den `+` tecken när det används med Curl.

    Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrollera status för jobbet, till exempel:

        {"id":"job_1415651640909_0026"}

3. Använd följande kommando för att kontrollera status för jobbet

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Ersätt `JOBID` med det värde som returneras i föregående steg. Exempel: om det returnera värdet var `{"id":"job_1415651640909_0026"}`, sedan `JOBID` är `job_1415651640909_0026`.

    Om jobbet har slutförts tillståndet är **lyckades**.

    > [!NOTE]
    > Den här Curl-begäran returnerar ett JavaScript Object Notation (JSON) dokument med information om jobbet och jq används för att hämta tillståndet värdet.

## <a id="results"></a>Visa resultat

När tillståndet för jobbet har ändrats till **lyckades**, du kan hämta resultatet av jobbet. Den `statusdir` -parameter som överförs med frågan innehåller platsen för utdatafilen; i det här fallet `/example/pigcurl`.

HDInsight kan använda antingen Azure Storage eller Azure Data Lake Store som standard-datalager. Det finns olika sätt att hämta data beroende på vad du använder. Mer information finns i avsnittet för lagring av den [information för Linux-baserade HDInsight](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) dokumentet.

## <a id="summary"></a>Sammanfattning

Du kan använda en rå HTTP-begäran för att köra, övervaka och visa resultatet av Pig-jobb i ditt HDInsight-kluster som visas i det här dokumentet.

Mer information om REST-gränssnitt som används i den här artikeln finns det [WebHCat referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Nästa steg

Allmän information om Pig i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)
