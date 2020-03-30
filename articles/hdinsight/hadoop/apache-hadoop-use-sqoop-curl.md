---
title: Använd Curl för att exportera data med Apache Sqoop i Azure HDInsight
description: Lär dig hur du skickar Apache Sqoop-jobb till Azure HDInsight med Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: da29785547d1b6eb4b38d07f020ba885dc5137ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767594"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Kör Apache Sqoop jobb i HDInsight med Curl

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Curl för att köra Apache Sqoop-jobb på ett Apache Hadoop-kluster i HDInsight. Den här artikeln visar hur du exporterar data från Azure Storage och importerar den till en SQL Server-databas med Curl. Denna artikel är en fortsättning [på Använda Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

Curl används för att visa hur du kan interagera med HDInsight med hjälp av råa HTTP-begäranden för att köra, övervaka och hämta resultaten av Sqoop-jobb. Detta fungerar med hjälp av WebHCat REST API (tidigare kallat Templeton) som tillhandahålls av ditt HDInsight-kluster.

## <a name="prerequisites"></a>Krav

* Slutförande av [Inrätta testmiljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* En klient som ska fråga Azure SQL Database. Överväg att använda [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio [Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl är ett verktyg för att överföra data från eller till ett HDInsight-kluster.

* [jq](https://stedolan.github.io/jq/). JQ-verktyget används för att bearbeta JSON-data som returneras från REST-begäranden.

* Förtrogenhet med Sqoop. Mer information finns i [Användarhandboken för Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Skicka Apache Sqoop jobb med hjälp av Curl

Använd Curl för att exportera data med Apache Sqoop-jobb från Azure Storage till SQL Server.

> [!NOTE]  
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör. Du måste också använda klustrets namn som en del av den URI (Uniform Resource Identifier) som används för att skicka begäranden till servern.

För kommandona i det `USERNAME` här avsnittet ersätter du med användaren `PASSWORD` att autentisera till klustret och ersätta med lösenordet för användarkontot. Ersätt `CLUSTERNAME` med namnet på klustret.

REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. För enkel användning, ställ in variablerna nedan. Det här exemplet baseras på en Windows-miljö, ändra efter behov för din miljö.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Du bör få ett svar som liknar följande:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Använd följande för att skicka in ett sqoop-jobb:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    De parametrar som används i det här kommandot är följande:

   * **-d** - `-G` Eftersom används inte, begäran standardvärden till POST-metoden. `-d`anger de datavärden som skickas med begäran.

       * **user.name** - Användaren som kör kommandot.

       * **kommando** - Kommandot Sqoop som ska köras.

       * **statusdir** - Den katalog som statusen för det här jobbet ska skrivas till.

     Det här kommandot returnerar ett jobb-ID som kan användas för att kontrollera jobbets status.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Om du vill kontrollera jobbets status använder du följande kommando. Ersätt `JOBID` med det värde som returnerades i föregående steg. Om returvärdet till exempel `{"id":"job_1415651640909_0026"}`var `JOBID` , `job_1415651640909_0026`skulle det vara . Ändra plats `jq` för vid behov.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Om jobbet är klart kommer tillståndet att **lyckas**.

   > [!NOTE]  
   > Den här Curl-begäran returnerar ett JSON-dokument (JavaScript Object Notation) med information om jobbet. jq används för att hämta endast tillståndsvärdet.

1. När jobbets tillstånd har **ändrats**till LYCKADES kan du hämta resultatet av jobbet från Azure Blob-lagring. Parametern `statusdir` som skickas med frågan innehåller platsen för utdatafilen. i detta `wasb:///example/data/sqoop/curl`fall, . Den här adressen lagrar utdata `example/data/sqoop/curl` för jobbet i katalogen på standardlagringsbehållaren som används av HDInsight-klustret.

    Du kan använda Azure-portalen för att komma åt stderr och stdout blobbar.

1. Om du vill kontrollera att data har exporterats använder du följande frågor från SQL-klienten för att visa exporterade data:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Begränsningar

* Massexport – Med Linux-baserad HDInsight stöder Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller Azure SQL Database för närvarande inte massinfogningar.
* Batching - Med Linux-baserade HDInsight, När du använder växeln `-batch` när du utför skär, kommer Sqoop utföra flera skär i stället för batching skäråtgärder.

## <a name="summary"></a>Sammanfattning

Som visas i det här dokumentet kan du använda en rå HTTP-begäran för att köra, övervaka och visa resultatet av Sqoop-jobb i DITT HDInsight-kluster.

Mer information om REST-gränssnittet som används i den här artikeln finns i <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API-guiden</a>.

## <a name="next-steps"></a>Nästa steg

[Använd Apache Sqoop med Apache Hadoop på HDInsight](hdinsight-use-sqoop.md)

För andra HDInsight artiklar som involverar curl:

* [Skapa Apache Hadoop-kluster med Azure REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Kör Apache Hive-frågor med Apache Hadoop i HDInsight med REST](apache-hadoop-use-hive-curl.md)
* [Kör MapReduce-jobb med Apache Hadoop på HDInsight med REST](apache-hadoop-use-mapreduce-curl.md)