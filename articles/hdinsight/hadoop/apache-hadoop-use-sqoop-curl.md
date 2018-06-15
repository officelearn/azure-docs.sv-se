---
title: Använda Hadoop Sqoop med Curl i HDInsight - Azure | Microsoft Docs
description: Lär dig mer om att skicka via fjärranslutning Sqoop jobb till HDInsight med Curl.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: a83b87f1ed052c6d21d337eb37bc560efbf118ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202153"
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Kör jobb för Sqoop med Hadoop i HDInsight med Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Curl för att köra Sqoop på ett Hadoop-kluster i HDInsight.

CURL används för att demonstrera hur du kan interagera med HDInsight med hjälp av rådata HTTP-begäranden att köra, övervaka och hämta resultaten för Sqoop jobb. Det här fungerar med WebHCat REST API (kallades tidigare Templeton) tillhandahålls av HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra stegen i den här artikeln:

* Fullständig [använda Sqoop med Hadoop i HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) att konfigurera en miljö med ett HDInsight-kluster och en Azure SQL-databas.
* [CURL](http://curl.haxx.se/). CURL är ett verktyg för att överföra data från eller till ett HDInsight-kluster.
* [jq](http://stedolan.github.io/jq/). Verktyget jq används för att bearbeta JSON-data som returneras från REST-begäranden.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Skicka Sqoop jobb med Curl
> [!NOTE]
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör. Du måste också använda klustrets namn som en del av den URI (Uniform Resource Identifier) som används för att skicka begäranden till servern.
> 
> För kommandona i det här avsnittet, ersätter du **USERNAME** med användaren för att autentisera till klustret och ersätter **PASSWORD** med lösenordet för användarkontot. Ersätt **CLUSTERNAME** med namnet på klustret.
> 
> REST API skyddas via [grundläggande autentisering](http://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.
> 
> 

1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Du bör få ett svar som liknar följande:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    De parametrar som används i det här kommandot är följande:
   
   * **-u** – Det användarnamn och lösenord som används för att autentisera begäran.
   * **-G** – Anger att detta är en GET-begäran.
     
     I början av URL-Adressen **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, är samma för alla begäranden. Sökvägen **/status**, anger att begäran ska returnera statusen WebHCat (även kallat Templeton) för servern. 
2. Använd följande för att skicka ett sqoop jobb:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    De parametrar som används i det här kommandot är följande:

    * **-d** - sedan `-G` används inte begäran som standard POST-metoden. `-d` Anger datavärdena som skickas med begäran.

        * **User.name** -användaren som kör kommandot.

        * **kommandot** -Sqoop för det kommando som ska köras.

        * **statusdir** -katalogen som status för jobbet kommer att skrivas till.

    Det här kommandot kan returnera ett jobb-ID som kan användas för att kontrollera status för jobbet.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Använd följande kommando för att kontrollera status för jobbet. Ersätt **JOBID** med värdet som returneras i föregående steg. Om exempelvis returvärde `{"id":"job_1415651640909_0026"}`, sedan **JOBID** skulle vara `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Om jobbet har slutförts, tillståndet blir **lyckades**.
   
   > [!NOTE]
   > Den här Curl-begäran returnerar ett JavaScript Object Notation (JSON) dokument med information om jobb. jq används för att hämta tillståndet värdet.
   > 
   > 
4. När status för jobbet har ändrats till **lyckades**, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` -parameter som överförs med frågan innehåller platsen för utdatafilen; i det här fallet **wasb: / / / exempel/data/sqoop/curl**. Den här adressen lagrar resultatet av jobbet i den **exempel/data/sqoop/curl** på standardbehållare för lagring som används av ditt HDInsight-kluster.
   
    Du kan använda Azure-portalen för att komma åt stderr och stdout blobbar.  Du kan också använda Microsoft SQL Server Management Studio för att kontrollera de data som överförs till tabellen log4jlogs.

## <a name="limitations"></a>Begränsningar
* Massinläsning export - med Linux-baserat HDInsight, Sqoop-kopplingen används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte bulkinfogningar.
* Batchbearbetning - med Linux-baserat HDInsight när du använder den `-batch` växeln när du utför infogningar, Sqoop utför flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="summary"></a>Sammanfattning
Som visas i det här dokumentet, kan du använda en rå HTTP-begäran att köra, övervaka och visa resultatet av Sqoop jobb på ditt HDInsight-kluster.

Mer information om REST-gränssnitt som används i den här artikeln finns det <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API-guiden</a>.

## <a name="next-steps"></a>Nästa steg
Allmän information om Hive med HDInsight:

* [Använda Sqoop med Hadoop i HDInsight](hdinsight-use-sqoop.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

För andra HDInsight curl artiklar som innefattar:
 
* [Skapa Hadoop-kluster med hjälp av Azure REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Köra Hive-frågor med Hadoop i HDInsight med hjälp av REST](apache-hadoop-use-hive-curl.md)
* [Kör jobb för MapReduce med Hadoop i HDInsight med hjälp av REST](apache-hadoop-use-mapreduce-curl.md)
* [Köra Pig med Hadoop på HDInsight med cURL](apache-hadoop-use-pig-curl.md)



