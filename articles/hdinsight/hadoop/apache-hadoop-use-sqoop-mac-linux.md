---
title: Apache Sqoop med Hadoop - Azure HDInsight
description: Lär dig hur du använder Apache Sqoop för att importera och exportera mellan Hadoop på HDInsight och en Azure SQL Database.
keywords: sqoop med hadoop, sqoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 6aa5627a0b5e8cb29f0ec68b948084a0bed30529
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259453"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Använd Apache Sqoop för att importera och exportera data mellan Hadoop på HDInsight och SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop för att importera och exportera mellan ett Hadoop-kluster i Azure HDInsight och Azure SQL Database eller Microsoft SQL Server-databasen. Stegen i det här dokumentet används de `sqoop` direkt från huvudnoden av Hadoop-kluster. Du kan använda SSH för att ansluta till klustrets huvudnod och kör kommandon i det här dokumentet.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar bara med HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> I det här dokumentet förutsätter vi att du redan har skapat en Azure SQL-databas med namnet `sqooptest`.
>
> Det här dokumentet innehåller instruktioner för T-SQL som används för att skapa och fråga en tabell i SQL-databas. Det finns många klienter som du kan använda dessa instruktioner med SQL Database. Vi rekommenderar följande klienter:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * Den [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) verktyget

## <a name="create-the-table-in-sql-database"></a>Skapa tabell i SQL-databas

> [!IMPORTANT]
> Om du använder HDInsight-klustret och SQL-databas som skapats i [skapa klustret och SQL database](hdinsight-use-sqoop.md), hoppa över stegen i det här avsnittet. Databasen och tabellen har skapats som en del av stegen i den [skapa klustret och SQL database](hdinsight-use-sqoop.md) dokumentet.

Använd en SQL-klient för att ansluta till den `sqooptest` din SQL database. Sedan använder följande T-SQL för att skapa en tabell med namnet `mobiledata`:

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Sqoop export

1. Använda SSH för att ansluta till HDInsight-kluster. Till exempel följande kommando ansluter till den primära huvudnoden av ett kluster med namnet `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Om du vill kontrollera att Sqoop kan se din SQL-databas, använder du följande kommando:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    När du uppmanas, anger du lösenordet för SQL Database-inloggningen.

    Det här kommandot returnerar en lista över databaser, inklusive den **sqooptest** database använde tidigare.

3. Att exportera data från Hive **hivesampletable** -tabellen till den **mobiledata** tabellen i SQL-databas, använder du följande kommando:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. För att verifiera att data exporterats, använder du följande fråga från SQL-klienten för att se exporterade data:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Det här kommandot visar en lista över 50 rader som har importerats i tabellen.

## <a name="sqoop-import"></a>Sqoop-import

1. Använd följande kommando för att importera data från den **mobiledata** tabellen i SQL-databas till den **wasb: / / / självstudier/usesqoop/importeddata** på HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Fält i datamängden avgränsas med ett tabbtecken och linjerna avslutas med ett tecken för ny rad.

    > [!IMPORTANT]
    > Den `wasb:///` sökvägen fungerar med kluster som använder Azure Storage som standardklusterlagringen. För kluster som använder Azure Data Lake Store kan använda `adl:///` i stället.

2. När importen har slutförts, använder du följande kommando för att visa data i den nya katalogen:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Med hjälp av SQLServer

Du kan också använda Sqoop för att importera och exportera data från SQL Server. Skillnaderna mellan att använda SQL Database och SQL Server är:

* Både HDInsight och SQL Server måste vara i samma Azure Virtual Network.

    Ett exempel finns i den [ansluta HDInsight till det lokala nätverket](./../connect-on-premises-network.md) dokumentet.

    Mer information om hur du använder HDInsight med Azure Virtual Network finns i den [utöka HDInsight med Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentet. Mer information om Azure-nätverk finns i den [översikt över Virtual Network](../../virtual-network/virtual-networks-overview.md) dokumentet.

* SQL Server måste konfigureras för att tillåta SQL-autentisering. Mer information finns i den [välja ett autentiseringsläge](https://msdn.microsoft.com/ms144284.aspx) dokumentet.

* Du kan behöva konfigurera SQL Server för att acceptera fjärranslutningar. Mer information finns i den [så här felsöker du ansluter till SQL Server database engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentet.

* Använd följande Transact-SQL-uttryck för att skapa den **mobiledata** tabell:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* När du ansluter till SQL Server från HDInsight, kan du behöva använda IP-adressen för SQL Server. Exempel:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Begränsningar

* Massinläsning exportera – med Linux-baserade HDInsight, Sqoop anslutningen används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder inte bulkinfogningar.

* Batchbearbetning - med Linux-baserade HDInsight när du använder den `-batch` växla när du utför infogningar, Sqoop gör flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Oozie med HDInsight](../hdinsight-use-oozie.md): Använd Sqoop åtgärden i ett Oozie-arbetsflöde.
* [Analysera flygförseningsdata med HDInsight](../hdinsight-analyze-flight-delay-data.md): använda Hive för att analysera flygning fördröjning data och Använd sedan Sqoop för att exportera data till en Azure SQL database.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): hitta andra metoder för att ladda upp data till HDInsight/Azure Blob storage.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
