---
title: Kopiera data mellan Azure Data Lake Storage Gen1 och Azure SQL database med Sqoop | Microsoft Docs
description: Använd Sqoop för att kopiera data mellan Azure SQL Database och Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 7d3283b03d15278d1f7fd42a72b154dab1a442b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878543"
---
# <a name="copy-data-between-azure-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopiera data mellan Azure Data Lake Storage Gen1 och Azure SQL database med Sqoop
Lär dig hur du använder Apache Sqoop för att importera och exportera data mellan Azure SQL Database och Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Vad är Sqoop?
Stordata-program är det naturliga valet för att bearbeta Ostrukturerade och delvis strukturerade data, till exempel och filerna. Det kan dock också finnas ett behov av att bearbeta strukturerade data som lagras i relationsdatabaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) är ett verktyg som utformats för att överföra data mellan relationella databaser och en lagringsplats för stordata, till exempel Data Lake Storage Gen1. Du kan använda den för att importera data från ett relationella databashanteringssystem (RDBMS) som Azure SQL Database till Data Lake Storage Gen1. Du kan omvandla och analysera data med big data-arbetsbelastningar och sedan exportera data tillbaka till en RDBMS. I den här självstudien använder du en Azure SQL Database som din relationsdatabas till import/export från.

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto med Azure Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Den här artikeln förutsätter att du har en HDInsight Linux-kluster med Data Lake Storage Gen1 åtkomst.
* **Azure SQL Database**. Anvisningar för hur du skapar ett finns i [skapa en Azure SQL database](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Lär du dig snabbt med videor?
[Den här videon](https://mix.office.com/watch/1butcdjxmu114) om hur du kopierar data mellan Azure Storage-Blobbar och Data Lake Storage Gen1 använda DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Skapa exemplet tabeller i Azure SQL Database
1. Börja med skapa två Exempeltabeller i Azure SQL Database. Använd [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio för att ansluta till Azure SQL Database och kör följande frågor.

    **Skapa tabell 1**

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
2. I **tabell1**, Lägg till exempeldata. Lämna **tabell2** tom. Vi ska importera data från **tabell1** i Data Lake Storage Gen1. Vi kommer sedan att exportera data från Data Lake Storage Gen1 till **tabell2**. Kör följande fragment.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Använda Sqoop från ett HDInsight-kluster med åtkomst till Data Lake Storage Gen1
Det finns redan de Sqoop paket som är tillgängliga för ett HDInsight-kluster. Om du har konfigurerat HDInsight-kluster för att använda Data Lake Storage Gen1 som ett ytterligare lagringsutrymme, du kan använda Sqoop (utan några konfigurationsändringar) för att importera/exportera data mellan en relationsdatabas (i det här exemplet, Azure SQL Database) och en Datasjö Gen1 lagringskonto.

1. I den här självstudiekursen förutsätter vi att du har skapat ett Linux-kluster, så du bör använda SSH för att ansluta till klustret. Se [ansluta till ett Linux-baserade HDInsight-kluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Kontrollera om du kan komma åt kontot Data Lake Storage Gen1 från klustret. Kör följande kommando från SSH-prompten:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

    Detta bör ge en lista över filer/mappar i Data Lake Storage Gen1-konto.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importera data från Azure SQL Database till Data Lake Storage Gen1
1. Gå till katalogen där Sqoop paket är tillgängliga. Det här är vanligtvis på `/usr/hdp/<version>/sqoop/bin`.
2. Importera data från **tabell1** till Data Lake Storage Gen1-kontot. Använd följande syntax:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Observera att **sql-database-server-name** platshållaren representerar namnet på den server där Azure SQL-databasen körs. **SQL-databasnamn** platshållaren representerar det faktiska databasnamnet.

    Exempel:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Kontrollera att data har överförts till Data Lake Storage Gen1-kontot. Kör följande kommando:

        hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Du bör se följande utdata.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Varje **del-m -*** fil motsvarar en rad i källtabellen, **tabell1**. Du kan visa innehållet i en del - m-* verifiera.


### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Exportera data från Data Lake Storage Gen1 till Azure SQL Database
1. Exportera data från Data Lake Storage Gen1 kontot till tabellen tom **tabell2**, i Azure SQL Database. Använd följande syntax.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Exempel:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Kontrollera att data har överförts till SQL-databastabell. Använd [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio för att ansluta till Azure SQL-databasen och kör sedan följande fråga.

        SELECT * FROM TABLE2

    Detta bör ha följande utdata.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Prestandaöverväganden när du använder Sqoop

Prestandajustering Sqoop jobbet för att kopiera data till Data Lake Storage Gen1, se [Sqoop prestanda dokumentet](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Se också
* [Kopiera data från Azure Storage BLOB till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
