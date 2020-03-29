---
title: Apache Sqoop med Apache Hadoop - Azure HDInsight
description: Lär dig hur du använder Apache Sqoop för att importera och exportera mellan Apache Hadoop på HDInsight och en Azure SQL-databas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769395"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Använda Apache Sqoop för att importera och exportera data mellan Apache Hadoop på HDInsight och SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop för att importera och exportera mellan ett Apache Hadoop-kluster i Azure HDInsight och Azure SQL Database eller Microsoft SQL Server-databas. Stegen i det här `sqoop` dokumentet använder kommandot direkt från headnoden för Hadoop-klustret. Du använder SSH för att ansluta till huvudnoden och köra kommandona i det här dokumentet. Denna artikel är en fortsättning [på Använda Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Krav

* Slutförande av [Inrätta testmiljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från [Använd Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Förtrogenhet med Sqoop. Mer information finns i [Användarhandboken för Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Konfigurera

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. För enkel användning, ange variabler. Ersätt `PASSWORD` `MYSQLSERVER`, `MYDATABASE` och med relevanta värden och ange sedan kommandona nedan:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop export

Från Hive till SQL Server.

1. Om du vill kontrollera att Sqoop kan se din SQL-databas anger du kommandot nedan i den öppna SSH-anslutningen. Det här kommandot returnerar en lista över databaser.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Ange följande kommando om du vill visa en lista över tabeller för den angivna databasen:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Om du vill exportera `hivesampletable` data `mobiledata` från tabellen Hive till tabellen i SQL Database anger du kommandot nedan i den öppna SSH-anslutningen:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Om du vill kontrollera att data har exporterats använder du följande frågor från SSH-anslutningen för att visa exporterade data:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop import

Från SQL Server till Azure-lagring.

1. Ange kommandot nedan i din öppna SSH-anslutning för att importera data från `mobiledata` tabellen i SQL Database, till katalogen `wasbs:///tutorials/usesqoop/importeddata` på HDInsight. Fälten i data avgränsas med ett tabbtecken och raderna avslutas med ett nytt radtecken.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Du kan också ange en Hive-tabell:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. När importen är klar anger du följande kommando i den öppna SSH-anslutningen för att lista ut data i den nya katalogen:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Använd [beeline för](./apache-hadoop-use-hive-beeline.md) att kontrollera att tabellen har skapats i Hive.

    1. Anslut

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Kör varje fråga under en i taget och granska utdata:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Avsluta beeline `!exit`med .

## <a name="limitations"></a>Begränsningar

* Massexport – Med Linux-baserad HDInsight stöder Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller Azure SQL Database inte massinfogningar.

* Batching - Med Linux-baserade HDInsight, När du använder växeln `-batch` när du utför skär, gör Sqoop flera skär istället för batching skäråtgärder.

## <a name="important-considerations"></a>Att tänka på

* Både HDInsight och SQL Server måste finnas i samma Virtuella Azure-nätverk.

    Ett exempel finns i [Connect HDInsight till ditt lokala nätverksdokument.](./../connect-on-premises-network.md)

    Mer information om hur du använder HDInsight med ett virtuellt Azure-nätverk finns i dokumentet [Utöka HDInsight med Azure Virtual Network.](../hdinsight-plan-virtual-network-deployment.md) Mer information om Azure Virtual Network finns i dokumentet [om översikt för virtuellt nätverk.](../../virtual-network/virtual-networks-overview.md)

* SQL Server måste vara konfigurerad för att tillåta SQL-autentisering. Mer information finns i dokumentet [Välj ett autentiseringsläge.](https://msdn.microsoft.com/ms144284.aspx)

* Du kanske måste konfigurera SQL Server för att acceptera fjärranslutningar. Mer information finns i [felsökningen av hur du felsöker anslutning till SQL Server-databasmotordokumentet.](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig att använda Sqoop. Du kan läsa mer här:

* [Använd Apache Oozie med HDInsight:](../hdinsight-use-oozie-linux-mac.md)Använd Sqoop-åtgärd i ett Oozie-arbetsflöde.
* [Analysera flygfördröjningsdata med HDInsight:](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)Använd interaktiv fråga för att analysera flygfördröjningsdata och använd sedan Sqoop för att exportera data till en Azure SQL-databas.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att överföra data till HDInsight/Azure Blob-lagring.
