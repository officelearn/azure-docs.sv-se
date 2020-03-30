---
title: Kopiera data mellan Data Lake Storage Gen1 och Azure SQL - Sqoop | Microsoft-dokument
description: Använda Sqoop för att kopiera data mellan Azure SQL Database och Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839064"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopiera data mellan Data Lake Storage Gen1 och Azure SQL Database med Sqoop

Lär dig hur du använder Apache Sqoop för att importera och exportera data mellan Azure SQL Database och Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Vad är Sqoop?

Big data-program är ett naturligt val för bearbetning av ostrukturerade och halvstrukturerade data, till exempel loggar och filer. Du kan dock också behöva bearbeta strukturerade data som lagras i relationsdatabaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) är ett verktyg som är utformat för att överföra data mellan relationsdatabaser och en stordatadatabas, till exempel Data Lake Storage Gen1. Du kan använda den för att importera data från ett relationsdatabashanteringssystem (RDBMS), till exempel Azure SQL Database till Data Lake Storage Gen1. Du kan sedan omvandla och analysera data med hjälp av arbetsbelastningar för stordata och sedan exportera tillbaka data till en RDBMS. I den här artikeln använder du en Azure SQL-databas som relationsdatabas för att importera/exportera från.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du ha följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar kontot finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [Skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Den här artikeln förutsätter att du har ett HDInsight Linux-kluster med Data Lake Storage Gen1-åtkomst.
* **Azure SQL-databas**. Instruktioner om hur du skapar en finns i [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Skapa exempeltabeller i Azure SQL-databasen

1. Starta genom att skapa två exempeltabeller i Azure SQL-databasen. Använd [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio för att ansluta till databasen och kör sedan följande frågor.

    **Skapa tabell1**

       CREATE TABLE [dbo].[Table1](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

    **Skapa tabell2**

       CREATE TABLE [dbo].[Table2](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

1. Kör följande kommando om du vill lägga till några exempeldata i **tabell1**. Lämna **tabell2** tomt. Senare importerar du data från **tabell1** till DataSjölagringgengen1. Sedan exporterar du data från Data Lake Storage Gen1 till **Tabell2**.

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Använda Sqoop från ett HDInsight-kluster med åtkomst till Data Lake Storage Gen1

Ett HDInsight-kluster har redan Sqoop-paket tillgängliga. Om du har konfigurerat HDInsight-klustret för att använda Data Lake Storage Gen1 som ytterligare lagring kan du använda Sqoop (utan några konfigurationsändringar) för att importera/exportera data mellan en relationsdatabas som Azure SQL Database och ett Data Lake Storage Gen1-konto.

1. I den här artikeln antar vi att du har skapat ett Linux-kluster så att du bör använda SSH för att ansluta till klustret. Se [Anslut till ett Linux-baserat HDInsight-kluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Kontrollera om du kan komma åt datasjölagringsgenm1-kontot från klustret. Kör följande kommando från SSH-prompten:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   Det här kommandot innehåller en lista över filer/mappar i datasjölagringsgenm1-kontot.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importera data från Azure SQL Database till Data Lake Storage Gen1

1. Navigera till katalogen där Sqoop-paket finns tillgängliga. Den här platsen `/usr/hdp/<version>/sqoop/bin`är vanligtvis .

1. Importera data från **tabell1** till datasjölagringsgenm1-kontot. Använd följande syntax:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   Platshållaren för **sql-database-server-name** representerar namnet på den server där Azure SQL-databasen körs. **platshållaren för sql-database-name** representerar det faktiska databasnamnet.

   Exempel:

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Kontrollera att data har överförts till datasjölagringsgenm1-kontot. Kör följande kommando:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   Du bör se följande utdata.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Varje **del-m-*** fil motsvarar en rad i källtabellen, **Tabell1**. Du kan visa innehållet i del-m-* filer att verifiera.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Exportera data från Data Lake Storage Gen1 till Azure SQL Database

1. Exportera data från datasjölagringsgenm1-kontot till den tomma **tabellen, Tabell2,** i Azure SQL Database. Använd följande syntax.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Exempel:

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Kontrollera att data har överförts till SQL Database-tabellen. Använd [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio för att ansluta till Azure SQL Database och kör sedan följande fråga.

       SELECT * FROM TABLE2

   Det här kommandot bör ha följande utdata.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Prestandaöverväganden när du använder Sqoop

Information om prestandajustering ditt Sqoop-jobb för att kopiera data till Data Lake Storage Gen1 finns i [blogginlägget om Sqoop-prestanda](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="next-steps"></a>Nästa steg

* [Kopiera data från Azure Storage Blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använda Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
