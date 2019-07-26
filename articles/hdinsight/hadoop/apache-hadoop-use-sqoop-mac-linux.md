---
title: Apache Sqoop med Apache Hadoop – Azure HDInsight
description: Lär dig hur du använder Apache Sqoop för att importera och exportera mellan Apache Hadoop på HDInsight och ett Azure SQL Database.
keywords: Hadoop Sqoop, Sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406041"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Använd Apache Sqoop för att importera och exportera data mellan Apache Hadoop på HDInsight och SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop för att importera och exportera mellan ett Apache Hadoop kluster i Azure HDInsight och Azure SQL Database eller Microsoft SQL Server Database. Stegen i det här dokumentet använder `sqoop` kommandot direkt från huvudnoden i Hadoop-klustret. Du kan använda SSH för att ansluta till Head-noden och köra kommandona i det här dokumentet. Den här artikeln är en fortsättning på [användning av Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Förutsättningar

* Slutför [konfiguration av test miljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från att [använda Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* En klient för att fråga Azure SQL-databasen. Överväg att använda [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) eller [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Sqoop-export

Från Hive till SQL Server.

1. Använd SSH för att ansluta till HDInsight-klustret. Ersätt `CLUSTERNAME` med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Ersätt `MYSQLSERVER` med namnet på din SQL Server. För att kontrol lera att Sqoop kan se SQL Database anger du kommandot nedan i den öppna SSH-anslutningen. Ange lösen ordet för den SQL Server inloggningen när du uppmanas till det. Det här kommandot returnerar en lista över databaser.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Ersätt `MYSQLSERVER` med namnet på din SQL Server och `MYDATABASE` med namnet på din SQL-databas. Om du vill exportera data från `hivesampletable` Hive-tabellen `mobiledata` till tabellen i SQL Database anger du kommandot nedan i den öppna ssh-anslutningen. Ange lösen ordet för den SQL Server inloggningen när du uppmanas till detta

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Verifiera att data har exporter ATS genom att använda följande frågor från din SQL-klient för att Visa exporterade data:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop-import

Från SQL Server till Azure Storage.

1. Ersätt `MYSQLSERVER` med namnet på din SQL Server och `MYDATABASE` med namnet på din SQL-databas. Ange kommandot nedan i den öppna ssh-anslutningen för att importera data från `mobiledata` tabellen i SQL Database `wasb:///tutorials/usesqoop/importeddata` till katalogen i HDInsight. Ange lösen ordet för den SQL Server inloggningen när du uppmanas till det. Fälten i data skiljs åt av ett tabbtecken och raderna avslutas med ett nytt rad-värde.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. När importen har slutförts anger du följande kommando i den öppna SSH-anslutningen för att skapa en lista över data i den nya katalogen:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Begränsningar

* Mass export – med Linux-baserat HDInsight har Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller Azure SQL Database inte stöd för Mass infogningar.

* Batching – med Linux-baserat HDInsight, och när du `-batch` använder växeln när du utför tillägg, gör Sqoop flera infogningar i stället för att batch-sätta in åtgärderna.

## <a name="important-considerations"></a>Viktiga överväganden

* Både HDInsight och SQL Server måste finnas på samma Azure-Virtual Network.

    Ett exempel finns i [ansluta HDInsight till ditt lokala nätverks](./../connect-on-premises-network.md) dokument.

    Mer information om hur du använder HDInsight med ett Azure-Virtual Network finns i avsnittet [utöka HDInsight med azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) Document. Mer information om Azure Virtual Network finns i [Virtual Network översikts](../../virtual-network/virtual-networks-overview.md) dokument.

* SQL Server måste konfigureras för att tillåta SQL-autentisering. Mer information finns i dokumentet [Välj ett autentiseringsläge](https://msdn.microsoft.com/ms144284.aspx) .

* Du kan behöva konfigurera SQL Server för att godkänna fjärr anslutningar. Mer information finns i [fel sökning av anslutning till SQL Server databas motor](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokument.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Apache Oozie med HDInsight](../hdinsight-use-oozie-linux-mac.md): Använd Sqoop-åtgärd i ett Oozie-arbetsflöde.
* [Analysera flyg fördröjnings data med HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Använd interaktiv fråga för att analysera flyg fördröjnings data och Använd sedan Sqoop för att exportera data till en Azure SQL-databas.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att ladda upp data till HDInsight/Azure Blob Storage.
