---
title: Apache Sqoop med Apache Hadoop - Azure HDInsight
description: Lär dig hur du använder Apache Sqoop för att importera och exportera mellan Apache Hadoop på HDInsight och en Azure SQL Database.
keywords: sqoop med hadoop, sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128982"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Använd Apache Sqoop för att importera och exportera data mellan Apache Hadoop på HDInsight och SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop för att importera och exportera mellan en Apache Hadoop-kluster i Azure HDInsight och Azure SQL Database eller Microsoft SQL Server-databasen. Stegen i det här dokumentet används de `sqoop` direkt från huvudnoden av Hadoop-kluster. Du kan använda SSH för att ansluta till klustrets huvudnod och kör kommandon i det här dokumentet. Den här artikeln är en förlängning av [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Slutförandet av [skapa testmiljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* En klient att fråga Azure SQL-databasen. Överväg att använda [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) eller [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Sqoop export

Från Hive till SQLServer.

1. Använda SSH för att ansluta till HDInsight-kluster. Ersätt `CLUSTERNAME` med namnet på ditt kluster, ange sedan kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Ersätt `MYSQLSERVER` med namnet på din SQL-Server. Om du vill kontrollera att Sqoop kan se din SQL-databas, anger du kommandot nedan i din öppna SSH-anslutning. Ange lösenordet för SQL Server-inloggning när du tillfrågas. Det här kommandot returnerar en lista över databaser.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Ersätt `MYSQLSERVER` med namnet på SQL Server, och `MYDATABASE` med namnet på din SQL-databas. Att exportera data från Hive `hivesampletable` -tabellen till den `mobiledata` tabellen i SQL-databas, skriver du kommandot nedan i din öppna SSH-anslutning. Ange lösenordet för SQL Server-inloggning när du uppmanas

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Använd följande frågor från SQL-klienten för att verifiera att data har exporterats till Se exporterade data:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop-import

Från SQL Server till Azure storage.

1. Ersätt `MYSQLSERVER` med namnet på SQL Server, och `MYDATABASE` med namnet på din SQL-databas. Ange kommandot nedan i din öppna SSH-anslutning för att importera data från den `mobiledata` tabellen i SQL-databas till den `wasb:///tutorials/usesqoop/importeddata` på HDInsight. Ange lösenordet för SQL Server-inloggning när du tillfrågas. Fält i datamängden avgränsas med ett tabbtecken och linjerna avslutas med ett tecken för ny rad.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. När importen är klar, anger du följande kommando i dina öppna SSH-anslutning för att visa data i den nya katalogen:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Begränsningar

* Massinläsning exportera – med Linux-baserade HDInsight, Sqoop anslutningen används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder inte bulkinfogningar.

* Batchbearbetning - med Linux-baserade HDInsight när du använder den `-batch` växla när du utför infogningar, Sqoop gör flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="important-considerations"></a>Att tänka på

* Både HDInsight och SQL Server måste vara i samma Azure Virtual Network.

    Ett exempel finns i den [ansluta HDInsight till det lokala nätverket](./../connect-on-premises-network.md) dokumentet.

    Mer information om hur du använder HDInsight med Azure Virtual Network finns i den [utöka HDInsight med Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentet. Mer information om Azure-nätverk finns i den [översikt över Virtual Network](../../virtual-network/virtual-networks-overview.md) dokumentet.

* SQL Server måste konfigureras för att tillåta SQL-autentisering. Mer information finns i den [välja ett autentiseringsläge](https://msdn.microsoft.com/ms144284.aspx) dokumentet.

* Du kan behöva konfigurera SQL Server för att acceptera fjärranslutningar. Mer information finns i den [så här felsöker du ansluter till SQL Server database engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentet.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Apache Oozie med HDInsight](../hdinsight-use-oozie-linux-mac.md): Använd Sqoop åtgärden i ett Oozie-arbetsflöde.
* [Analysera flygförseningsdata med HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): Använda Apache Hive för att analysera flygförseningsdata och Använd sedan Sqoop för att exportera data till en Azure SQL database.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att ladda upp data till HDInsight/Azure Blob storage.
