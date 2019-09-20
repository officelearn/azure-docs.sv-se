---
title: Använd Apache Spark för att läsa och skriva data till Azure SQL Database
description: Lär dig hur du konfigurerar en anslutning mellan HDInsight Spark-kluster och en Azure SQL-databas för att läsa data, skriva data och strömma data till en SQL-databas
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 74bff4059442c85cfcde589c5a6cc7ab36472881
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147044"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Använd HDInsight Spark-kluster för att läsa och skriva data till Azure SQL Database

Lär dig hur du ansluter ett Apache Spark kluster i Azure HDInsight med en Azure SQL-databas och sedan läser, skriver och strömmar data till SQL-databasen. Anvisningarna i den här artikeln använder en [Jupyter Notebook](https://jupyter.org/) för att köra Scala-kodfragment. Du kan dock skapa ett fristående program i Scala eller python och utföra samma uppgifter.

## <a name="prerequisites"></a>Förutsättningar

* **Azure HDInsight Spark kluster**.  Följ instruktionerna i [skapa ett Apache Spark kluster i HDInsight](apache-spark-jupyter-spark-sql.md).

* **Azure SQL Database**. Följ instruktionerna i [skapa en Azure SQL-databas](../../sql-database/sql-database-get-started-portal.md). Se till att du skapar en databas med exemplet **AdventureWorksLT** schema och data. Se också till att du skapar en brand Väggs regel på server nivå så att klientens IP-adress kan komma åt SQL-databasen på-servern. Anvisningarna för att lägga till brand Väggs regeln finns i samma artikel. När du har skapat din Azure SQL-databas ska du se till att du behåller följande värden. Du behöver dem för att kunna ansluta till databasen från ett Spark-kluster.

    * Server namn som är värd för Azure SQL-databasen.
    * Namn på Azure SQL Database.
    * Användar namn/lösen ord för Azure SQL Database-administratör.

* **SQL Server Management Studio**. Följ anvisningarna i [använda SSMS för att ansluta och fråga efter data](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter Notebook 

Börja med att skapa en [Jupyter Notebook](https://jupyter.org/) som är associerad med Spark-klustret. Du använder den här antecknings boken för att köra kodfragment som används i den här artikeln. 

1. Öppna klustret från [Azure Portal](https://portal.azure.com/).
1. Välj **Jupyter Notebook** under **kluster instrument paneler** på höger sida.  Om du inte ser **kluster instrument paneler**väljer du **Översikt** på den vänstra menyn. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    ![Jupyter Notebook på Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter Notebook på Spark")
   
   > [!NOTE]  
   > Du kan också komma åt Jupyter Notebook i Spark-kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. I den Jupyter Notebook, i det övre högra hörnet, klickar du på **nytt**och sedan på **Spark** för att skapa en Scala-anteckningsbok. Jupyter-anteckningsböcker i HDInsight Spark-kluster tillhandahåller även **PySpark** -kärnan för Python2-program och **PySpark3** -kärnan för python3-program. I den här artikeln skapar vi en Scala Notebook.
   
    ![Kernels för Jupyter Notebook på Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels för Jupyter Notebook på Spark")

    Mer information om dessa kernlar finns i [Använda kernlar i Jupyter-anteckningsböcker med Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > I den här artikeln använder vi en spark-kernel (Scala) eftersom strömmande data från Spark till SQL Database bara stöds i Scala och Java. Även om du kan läsa från och skriva till SQL kan du göra det med hjälp av python, för konsekvens i den här artikeln använder vi Scala för alla tre åtgärderna.

1. Då öppnas en ny antecknings bok med ett standard namn ( **Namnlös**). Klicka på antecknings bokens namn och ange önskat namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för anteckningsboken")

Nu kan du börja skapa ditt program.
    
## <a name="read-data-from-azure-sql-database"></a>Läs data från Azure SQL Database

I det här avsnittet ska du läsa data från en tabell (till exempel **SalesLT. address**) som finns i AdventureWorks-databasen.

1. I en ny Jupyter-anteckningsbok, i en kod cell, klistrar du in följande kodfragment och ersätter plats hållar värden med värdena för din Azure SQL-databas.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

1. Använd kodfragmentet nedan för att skapa en JDBC-URL som du kan skicka till Spark dataframe-API `Properties` : erna skapar ett objekt som ska innehålla parametrarna. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Använd kodfragmentet nedan för att skapa en dataframe med data från en tabell i din Azure SQL-databas. I det här kodfragmentet använder vi en **SalesLT. address-** tabell som är tillgänglig som en del av **AdventureWorksLT** -databasen. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Nu kan du utföra åtgärder på dataframe, till exempel hämta data schemat:

       sqlTableDF.printSchema
   
    Du ser utdata som liknar följande:

    ![schemats utdata](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "schemats utdata")

1. Du kan också utföra åtgärder, t. ex. Hämta de 10 översta raderna.

       sqlTableDF.show(10)

1. Du kan också hämta vissa kolumner från data uppsättningen.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Skriva data till Azure SQL Database

I det här avsnittet använder vi en exempel-CSV-fil som är tillgänglig i klustret för att skapa en tabell i Azure SQL Database och fylla den med data. Exempel-CSV-filen (**HVAC. csv**) är tillgänglig i alla HDInsight-kluster `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`på.

1. I en ny Jupyter-anteckningsbok, i en kod cell, klistrar du in följande kodfragment och ersätter plats hållar värden med värdena för din Azure SQL-databas.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

1. Följande kodfragment skapar en JDBC-URL som du kan skicka till Spark dataframe-API: erna `Properties` skapar ett objekt som ska innehålla parametrarna. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Använd följande kodfragment för att extrahera schemat för data i HVAC. csv och använda schemat för att läsa in data från CSV- `readDf`filen i en dataframe. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Använd dataframe för att skapa en tillfällig `temphvactable`tabell. `readDf` Använd sedan den temporära tabellen för att skapa en Hive `hvactable_hive`-tabell.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Använd slutligen Hive-tabellen för att skapa en tabell i Azure SQL Database. Följande fragment skapar `hvactable` du i Azure SQL Database.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Anslut till Azure SQL-databasen med SSMS och kontrol lera att du ser `dbo.hvactable` en där.

    a. Starta SSMS och Anslut till Azure SQL Database genom att tillhandahålla anslutnings information som visas på skärm bilden nedan.

    ![Ansluta till SQL Database med SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Ansluta till SQL Database med SSMS1")

    b. Från Object Explorer expanderar du Azure SQL-databasen och noden tabell för att se **dbo. hvactable** som skapats.

    ![Ansluta till SQL Database med SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Ansluta till SQL Database med SSMS2")

1. Kör en fråga i SSMS för att se kolumnerna i tabellen.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Strömma data till Azure SQL Database

I det här avsnittet strömmar vi data till **hvactable** som du redan har skapat i Azure SQL Database i föregående avsnitt.

1. Det första steget är att se till att det inte finns några poster i **hvactable**. Kör följande fråga i tabellen med hjälp av SSMS.

       TRUNCATE TABLE [dbo].[hvactable]

1. Skapa en ny Jupyter-anteckningsbok i HDInsight Spark-klustret. Klistra in följande kodfragment i en Code-cell och tryck sedan på **SKIFT + RETUR**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Vi strömmar data från **HVAC. csv** till hvactable. HVAC. csv-filen är tillgänglig i klustret på `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`. I följande kodfragment hämtar vi först schemat för de data som ska strömmas. Sedan skapar vi en strömmande dataframe med det schemat. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. Utdata visar schemat för **HVAC. csv**. **Hvactable** har också samma schema. Utdata visar kolumnerna i tabellen.

    ![hdinsight Apache Spark schema tabell](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schema för tabell")

1. Använd slutligen följande kodfragment för att läsa data från HVAC. csv och strömma dem till **hvactable** i Azure SQL Database. Klistra in kodfragmentet i en kod cell, Ersätt plats hållarnas värden med värdena för din Azure SQL-databas och tryck sedan på **SKIFT + RETUR** för att köra.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

1. Kontrol lera att data strömmas i **hvactable** genom att köra följande fråga i SQL Server Management Studio (SSMS). Varje gång du kör frågan visas antalet rader i tabellen som ökar.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Nästa steg

* [Använd HDInsight Spark-kluster för att analysera data i Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Bearbeta strukturerade strömnings händelser med hjälp av EventHub](apache-spark-eventhub-structured-streaming.md)
* [Använda Apache Spark strukturerad strömning med Apache Kafka på HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
