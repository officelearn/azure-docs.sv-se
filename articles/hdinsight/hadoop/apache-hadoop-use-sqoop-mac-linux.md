---
title: Apache Sqoop med Hadoop - Azure HDInsight | Microsoft Docs
description: "Lär dig hur du använder Apache Sqoop för att importera och exportera mellan Hadoop i HDInsight och en Azure SQL Database."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: larryfr
ms.openlocfilehash: 250fb1dfed5cdab5308c2d91744e0cf051c32ccc
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Använd Apache Sqoop för att importera och exportera data mellan Hadoop i HDInsight och SQL-databas

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop för att importera och exportera mellan ett Hadoop-kluster i Azure HDInsight och Azure SQL Database eller Microsoft SQL Server-databasen. Stegen i det här dokumentet används de `sqoop` direkt från headnode av Hadoop-kluster. Du kan använda SSH för att ansluta till huvudnod och kör kommandona i det här dokumentet.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar endast med HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> Anvisningarna i det här dokumentet förutsätter att du redan har skapat en Azure SQL-databas med namnet `sqooptest`.
>
> Det här dokumentet innehåller T-SQL-uttryck som används för att skapa och fråga en tabell i SQL-databas. Det finns många klienter som du kan använda dessa instruktioner med SQL-databas. Vi rekommenderar följande klienter:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * Den [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) verktyget.

## <a name="create-the-table-in-sql-database"></a>Skapa tabellen i SQL-databas

> [!IMPORTANT]
> Om du använder HDInsight-klustret och SQL-databas som skapats i [skapa klustret och SQL database](hdinsight-use-sqoop.md), hoppa över stegen i det här avsnittet. Databasen och tabellen har skapats som en del av stegen i den [skapa klustret och SQL database](hdinsight-use-sqoop.md) dokumentet.

Använd en SQL-klient för att ansluta till den `sqooptest` databas i SQL-databasen. Använd följande T-SQL för att skapa en tabell med namnet `mobiledata`:

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

1. Använda SSH för att ansluta till HDInsight-klustret. Till exempel följande kommando ansluter till den primära headnode i ett kluster med namnet `mycluster`:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Om du vill kontrollera att Sqoop kan se din SQL-databas, använder du följande kommando:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    När du uppmanas, ange lösenordet för inloggningen för SQL-databas.

    Det här kommandot returnerar en lista över databaser, inklusive den **sqooptest** databas som används för tidigare.

3. Exportera data från registreringsdatafilen **hivesampletable** tabellen den **mobiledata** tabell i SQL-databas, använder du följande kommando:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Använd följande fråga i SQL-klient för att verifiera att data exporterats för att visa exporterade data:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;"
    ```

    Det här kommandot visar 50 rader som har importerats i tabellen.

## <a name="sqoop-import"></a>Sqoop import

1. Använd följande kommando för att importera data från den **mobiledata** tabell i SQL-databas till den **wasb: / / / självstudier/usesqoop/importeddata** på HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Fält i datamängden avgränsas med ett tabbtecken och raderna som avslutas med ett tecken för ny rad.

    > [!IMPORTANT]
    > Den `wasb:///` sökvägen fungerar med kluster som använder Azure Storage som standard klusterlagringen. För kluster som använder Azure Data Lake Store använder `adl:///` i stället.

2. När importen är klar använder du följande kommando för att lista ut data i den nya katalogen:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Använda SQLServer

Du kan också använda Sqoop för att importera och exportera data från SQL Server. Det är skillnad mellan att använda SQL Database och SQL Server:

* HDInsight- och SQL Server måste finnas på samma virtuella Azure-nätverk.

    Ett exempel finns i [ansluta HDInsight till ditt lokala nätverk](./../connect-on-premises-network.md) dokumentet.

    Mer information om hur du använder HDInsight med ett virtuellt Azure-nätverk finns i [utöka HDInsight med Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentet. Mer information om virtuella Azure-nätverk finns i [översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md) dokumentet.

* SQL Server måste konfigureras så att SQL-autentisering. Mer information finns i [välja ett autentiseringsläge](https://msdn.microsoft.com/ms144284.aspx) dokumentet.

* Du kan behöva konfigurera SQL Server för att acceptera fjärranslutningar. Mer information finns i [felsökning av anslutning till SQL Server-databasmotorn](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentet.

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

* När du ansluter till SQL Server från HDInsight kan kanske du använda IP-adressen för SQL Server. Exempel:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P <adminPassword> -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Begränsningar

* Massinläsning export - med Linux-baserat HDInsight, Sqoop-kopplingen används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder inte bulkinfogningar.

* Batchbearbetning - med Linux-baserat HDInsight när du använder den `-batch` växeln när du utför infogningar, Sqoop gör flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Oozie med HDInsight](../hdinsight-use-oozie.md): Använd Sqoop åtgärd i ett arbetsflöde för Oozie.
* [Analysera svarta fördröjning data med HDInsight](../hdinsight-analyze-flight-delay-data.md): använda Hive att analysera svarta fördröjning data och sedan använda Sqoop för att exportera data till en Azure SQL database.
* [Överföra data till HDInsight](../hdinsight-upload-data.md): hitta andra metoder för att överföra data till HDInsight-/ Azure Blob storage.

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
