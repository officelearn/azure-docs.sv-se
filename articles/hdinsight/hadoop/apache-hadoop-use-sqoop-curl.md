---
title: Använd Curl till att exportera data med Apache Sqoop i Azure HDInsight
description: Lär dig mer om att skicka via en fjärranslutning Apache Sqoop jobb till HDInsight med Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 345f492c5b2c754cbbcfa150561ee06b5a4154a5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126961"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Kör Apache Sqoop jobb i HDInsight med Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Curl för att köra Apache Sqoop jobb på en Apache Hadoop-kluster i HDInsight. Den här artikeln visar hur du exporterar data från Azure storage och importera den till en SQL Server-databas med Curl. Den här artikeln är en förlängning av [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

CURL används för att demonstrera hur du kan interagera med HDInsight med hjälp av rådata HTTP-begäranden att köra, övervaka och hämta resultaten för Sqoop jobb. Detta fungerar med WebHCat REST API (tidigare kallat Templeton) tillhandahålls av ditt HDInsight-kluster.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Slutförandet av [skapa testmiljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* En klient att fråga Azure SQL-databasen. Överväg att använda [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) eller [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [cURL](https://curl.haxx.se/). CURL är ett verktyg för att överföra data från eller till ett HDInsight-kluster.

* [jq](https://stedolan.github.io/jq/). Verktyget jq används för att bearbeta JSON-data som returneras från REST-begäranden.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Skicka Apache Sqoop jobb med hjälp av Curl

Använda Curl för att exportera data med hjälp av Apache Sqoop jobb från Azure storage till SQL Server.

> [!NOTE]  
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör. Du måste också använda klustrets namn som en del av den URI (Uniform Resource Identifier) som används för att skicka begäranden till servern.

Kommandona i det här avsnittet, ersätter `USERNAME` med användaren för att autentisera för klustret och Ersätt `PASSWORD` med lösenordet för användarkontot. Ersätt `CLUSTERNAME` med namnet på klustret.
 
REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Du bör få ett svar som liknar följande:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Ersätt `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, `SQLDATABASENAME` med lämpliga värden från kraven. Använd följande för att skicka ett sqoop jobb:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    De parametrar som används i det här kommandot är följande:

   * **-d** – sedan `-G` används inte begäran som standard POST-metoden. `-d` Anger de datavärden som skickas med begäran.

       * **User.name** -användaren som kör kommandot.

       * **kommandot** – The Sqoop kommando som ska köras.

       * **statusdir** -katalogen som status för det här jobbet kommer att skrivas till.

     Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrollera status för jobbet.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Använd följande kommando för att kontrollera status för jobbet. Ersätt `JOBID` med det värde som returneras i föregående steg. Exempel: om det returnera värdet var `{"id":"job_1415651640909_0026"}`, sedan `JOBID` skulle vara `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Om jobbet har slutförts tillståndet blir **lyckades**.
   
   > [!NOTE]  
   > Den här Curl-begäran returnerar ett JavaScript Object Notation (JSON) dokument med information om jobb. jq används för att hämta tillståndet värdet.

4. När tillståndet för jobbet har ändrats till **lyckades**, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` -parameter som överförs med frågan innehåller platsen för utdatafilen; i det här fallet `wasb:///example/data/sqoop/curl`. Den här adressen lagrar utdata för jobbet i den `example/data/sqoop/curl` på standardbehållare för lagring som används av ditt HDInsight-kluster.

    Du kan använda Azure-portalen för att få åtkomst till stderr och stdout blobar.

5. Använd följande frågor från SQL-klienten för att verifiera att data har exporterats till Se exporterade data:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Begränsningar
* Massinläsning exportera – med Linux-baserade HDInsight, Sqoop anslutningen används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte bulkinfogningar.
* Batchbearbetning - med Linux-baserade HDInsight när du använder den `-batch` växlar när du utför infogningar och Sqoop utför flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="summary"></a>Sammanfattning
Du kan använda en rå HTTP-begäran för att köra, övervaka och visa resultatet av Sqoop jobb i ditt HDInsight-kluster som visas i det här dokumentet.

Mer information om REST-gränssnitt som används i den här artikeln finns det <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API-guiden</a>.

## <a name="next-steps"></a>Nästa steg
[Använd Apache Sqoop med Apache Hadoop i HDInsight](hdinsight-use-sqoop.md)

För andra HDInsight curl artiklar som rör:
 
* [Skapa Apache Hadoop-kluster med Azure REST-API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Kör Apache Hive-frågor med Apache Hadoop i HDInsight med hjälp av REST](apache-hadoop-use-hive-curl.md)
* [Kör MapReduce-jobb med Apache Hadoop på HDInsight med hjälp av REST](apache-hadoop-use-mapreduce-curl.md)
* [Kör Apache Pig-jobb med Apache Hadoop på HDInsight med cURL](apache-hadoop-use-pig-curl.md)
