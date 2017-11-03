---
title: Kopiera data mellan Data Lake Store och Azure SQL database med Sqoop | Microsoft Docs
description: "Använd Sqoop för att kopiera data mellan Azure SQL Database och Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: ac73006f3589bf6d8adb0d7935b694b444d1415c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Kopiera data mellan Data Lake Store och Azure SQL database med Sqoop
Lär dig hur du använder Apache Sqoop för att importera och exportera data mellan Azure SQL Database och Data Lake Store.

## <a name="what-is-sqoop"></a>Vad är Sqoop?
Stordataprogram är en fysisk val för bearbetning av Ostrukturerade och halvstrukturerade data, till exempel loggar och filer. Men kan det också vara nödvändigt att bearbeta strukturerade data som lagras i relationsdatabaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) är ett verktyg som utformats för att överföra data mellan relationsdatabaser och en lagringsplats för stordata, till exempel Data Lake Store. Du kan använda den för att importera data från ett relationella databashanteringssystem (RDBMS), till exempel Azure SQL Database till Data Lake Store. Du kan sedan transformera och analysera data med stordataarbetsbelastningar och exportera data till en RDBMS. I den här kursen använder du en Azure SQL Database som relationell databas för att importera och exportera från.

## <a name="prerequisites"></a>Krav
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Store-konto. Se [skapar ett HDInsight-kluster med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Den här artikeln förutsätter att du har ett HDInsight Linux-kluster med Data Lake Store-åtkomst.
* **Azure SQL Database**. Anvisningar om hur du skapar en finns [skapa en Azure SQL database](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Lär du dig snabbt med videor?
[Det här videoklippet](https://mix.office.com/watch/1butcdjxmu114) om hur du kopierar data mellan Azure Storage-Blobbar och Data Lake Store med hjälp av DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Skapa Exempeltabeller i Azure SQL Database
1. Börja med, skapa två Exempeltabeller i Azure SQL Database. Använd [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio för att ansluta till Azure SQL-databasen och kör sedan följande frågor.

    **Skapa tabell 1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
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
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. I **tabell1**, lägga till exempeldata. Lämna **tabell2** tom. Vi kommer att importera data från **tabell1** i Data Lake Store. Vi kommer sedan att exportera data från Data Lake Store i **tabell2**. Kör följande kodavsnitt.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Använda Sqoop från ett HDInsight-kluster med åtkomst till Data Lake Store
Ett HDInsight-kluster har redan de Sqoop paket som är tillgängliga. Om du har konfigurerat HDInsight-klustret för att använda Data Lake Store som ett ytterligare lagringsutrymme, du kan använda Sqoop (utan några konfigurationsändringar) för att importera och exportera data mellan en relationsdatabas (i det här exemplet Azure SQL Database) och ett Data Lake Store-konto.

1. Den här självstudiekursen förutsätter vi att du har skapat ett Linux-kluster, så du bör använda SSH för att ansluta till klustret. Se [Anslut till ett Linux-baserat HDInsight-kluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Kontrollera om du har åtkomst till Data Lake Store-konto från klustret. Kör följande kommando från SSH-prompten:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Detta bör ge en lista över filer/mappar i Data Lake Store-konto.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importera data från Azure SQL Database till Data Lake Store
1. Gå till den katalog där Sqoop paket är tillgängliga. Vanligtvis detta kommer att vara `/usr/hdp/<version>/sqoop/bin`.
2. Importera data från **tabell1** i Data Lake Store-konto. Använd följande syntax:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Observera att **sql-server-databasnamn** platshållare representerar namnet på den server där Azure SQL-databasen körs. **SQL-databasnamnet** platshållare representerar det faktiska databasnamnet.

    Exempel:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Kontrollera att data har överförts till Data Lake Store-konto. Kör följande kommando:

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Du bör se följande utdata.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Varje **del-m -*** fil motsvarar en rad i tabellen källa **tabell1**. Du kan visa innehållet i en del - m-* verifiera.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exportera data från Data Lake Store till Azure SQL Database
1. Exportera data från Data Lake Store-konto till tom tabell **tabell2**, i Azure SQL Database. Använd följande syntax.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Exempel:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Kontrollera att data överfördes till SQL-databastabell. Använd [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio för att ansluta till Azure SQL-databasen och kör sedan följande fråga.

        SELECT * FROM TABLE2

    Detta bör ha följande utdata.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Prestandaöverväganden när du använder Sqoop

Prestandajustering Sqoop jobbet för att kopiera data till Data Lake Store, se [Sqoop prestanda dokumentet](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Se även
* [Kopiera data från Azure Storage-Blobbar till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
