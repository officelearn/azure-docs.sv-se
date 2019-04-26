---
title: Använda Apache Spark för att läsa och skriva data till Azure SQL database
description: Lär dig hur du konfigurerar en anslutning mellan HDInsight Spark-kluster och en Azure SQL database skriver data och strömdata till en SQL-databas om du vill läsa data
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/15/2018
ms.openlocfilehash: 2b818350c19d1d9ff34bcdac0e438def0f859e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60327045"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Använda HDInsight Spark-kluster för att läsa och skriva data till Azure SQL database

Lär dig hur du ansluter ett Apache Spark-kluster i Azure HDInsight med en Azure SQL database och sedan läsa, skriva och strömma data till SQL-databasen. Anvisningarna i den här artikeln används en [Jupyter Notebook](https://jupyter.org/) att köra kodfragmenten Scala. Du kan dock skapa ett fristående program i Scala och Python och utföra samma uppgifter. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure HDInsight Spark-kluster**.  Följ anvisningarna på [skapar ett Apache Spark-kluster i HDInsight](apache-spark-jupyter-spark-sql.md).

* **Azure SQL-databas**. Följ anvisningarna på [skapa en Azure SQL database](../../sql-database/sql-database-get-started-portal.md). Se till att skapa en databas med exemplet **AdventureWorksLT** schema och data. Kontrollera också att du skapar en brandväggsregel på servernivå så att din klients IP-adress att få åtkomst till SQL-databas på servern. Anvisningarna för att lägga till brandväggsregeln är tillgänglig i samma artikel. När du har skapat din Azure SQL-databas, se till att du behåller följande värden till hands. Du behöver dem för att ansluta till databasen från en Spark-kluster.

    * Servernamn som är värd för Azure SQL-databasen.
    * Azure SQL-databasnamn.
    * Azure SQL database administratörsanvändarnamn / lösenord.

* **SQL Server Management Studio**. Följ anvisningarna på [Använd SSMS för att ansluta och fråga data](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok 

Börja med att skapa en [Jupyter Notebook](https://jupyter.org/) som är associerade med Spark-klustret. Du kan använda den här anteckningsboken för att köra kodavsnitt som används i den här artikeln. 

1. Från den [Azure-portalen](https://portal.azure.com/), öppna ditt kluster.
1. Välj **Jupyter-anteckningsbok** under **Klusterinstrumentpaneler** på höger sida.  Om du inte ser **Klusterinstrumentpaneler**, klickar du på **översikt** menyn till vänster på bladet. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    ![Jupyter notebook i Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter notebook i Spark")
   
   > [!NOTE]  
   > Du kan också nå Jupyter notebook i Spark-kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. I Jupyter-anteckningsboken i det övre högra hörnet, klickar du på **New**, och klicka sedan på **Spark** att skapa en Scala-anteckningsbok. Jupyter-anteckningsböcker på HDInsight Spark-kluster tillhandahåller även den **PySpark** kernel för Python2-program och **PySpark3** kärnan i Python3 program. I den här artikeln skapar vi en Scala notebook.
   
    ![Kärnor för Jupyter notebook i Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "kärnor för Jupyter notebook i Spark")

    Mer information om dessa kernlar finns i [Använda kernlar i Jupyter-anteckningsböcker med Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > I den här artikeln använder vi en Spark (Scala) kernel eftersom strömmande data från Spark i SQL database stöds endast för närvarande i Scala och Java. Trots att läsa från och skriva till SQL kan göras med hjälp av Python, för att få konsekvens i den här artikeln, vi använder Scala för alla tre aktiviteter.

1. Då öppnas en ny anteckningsbok med ett standardnamn **Namnlös**. Klicka på anteckningsbokens namn och ange ett valfritt namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för anteckningsboken")

Nu kan du börja skapa ditt program.
    
## <a name="read-data-from-azure-sql-database"></a>Läsa data från Azure SQL-databas

I det här avsnittet ska du läsa data från en tabell (till exempel **SalesLT.Address**) som finns i AdventureWorks-databas.

1. Klistra in följande kodfragment i en ny Jupyter-anteckningsbok i en kodcell och Ersätt platshållarvärdena med värden för din Azure SQL-databas.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

1. Använd kodavsnittet nedan för att skapa en JDBC-URL som du kan skicka till Spark-dataframe API: er som skapar en `Properties` objekt för parametrarna. Klistra in kodfragmentet i en kodcell och tryck på **SKIFT + RETUR** ska köras.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Använd kodavsnittet nedan för att skapa en dataframe med data från en tabell i Azure SQL database. I det här kodfragmentet använder vi en **SalesLT.Address** tabell som är tillgängliga som en del av den **AdventureWorksLT** databas. Klistra in kodfragmentet i en kodcell och tryck på **SKIFT + RETUR** ska köras.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Du kan nu utföra åtgärder på nu när dataramen, till exempel komma dataschemat:

       sqlTableDF.printSchema
   
    Du ser utdata som liknar följande:

    ![Ange ett namn för anteckningsboken](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Ange ett namn för anteckningsboken")

1. Du kan också utföra åtgärder som hämtar de översta 10 raderna.

       sqlTableDF.show(10)

1. Alternativt kan du hämta specifika kolumner i datauppsättningen.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Skriva data till Azure SQL-databas

I det här avsnittet använder vi en CSV-exempelfil finns i klustret och skapa en tabell i Azure SQL-databas som du kan fylla det med data. Exempel-CSV-filen (**HVAC.csv**) är tillgänglig på alla HDInsight-kluster på `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Klistra in följande kodfragment i en ny Jupyter-anteckningsbok i en kodcell och Ersätt platshållarvärdena med värden för din Azure SQL-databas.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

1. Följande kodfragment skapar en JDBC-URL som du kan skicka till Spark-dataframe API: er som skapar en `Properties` objekt för parametrarna. Klistra in kodfragmentet i en kodcell och tryck på **SKIFT + RETUR** ska köras.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Använd följande kodfragment för att extrahera schemat för data i HVAC.csv och Använd schemat för att läsa in data från CSV-filen i en dataram `readDf`. Klistra in kodfragmentet i en kodcell och tryck på **SKIFT + RETUR** ska köras.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Använd den `readDf` dataframe att skapa en tillfällig tabell `temphvactable`. Sedan använder den temporära tabellen för att skapa en hive-tabell `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Använd slutligen hive-tabell för att skapa en tabell i Azure SQL database. Följande fragment skapar `hvactable` i Azure SQL database.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Anslut till Azure SQL-databasen med hjälp av SSMS och kontrollera att du ser en `dbo.hvactable` det.

    a. Starta SSMS och Anslut till Azure SQL-databasen genom att tillhandahålla anslutningsinformation som visas i skärmbilden nedan.

    ![Anslut till SQL-databas med SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Anslut till SQL-databas med SSMS")

    b. I Object Explorer expanderar du Azure SQL-databasen och noden tabell för att se den **dbo.hvactable** skapas.

    ![Anslut till SQL-databas med SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Anslut till SQL-databas med SSMS")

1. Köra en fråga i SSMS att visa kolumnerna i tabellen.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Stream-data till Azure SQL-databas

I det här avsnittet ska vi strömma data till den **hvactable** att du redan skapat i Azure SQL database i föregående avsnitt.

1. Som ett första steg, kontrollera att det finns inga poster i den **hvactable**. Med hjälp av SSMS, kör följande fråga i tabellen.

       DELETE FROM [dbo].[hvactable]

1. Skapa en ny Jupyter-anteckningsbok i HDInsight Spark-klustret. Klistra in följande kodfragment i en kodcell och tryck sedan på **SKIFT + RETUR**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Vi strömma data från den **HVAC.csv** till hvactable. HVAC.csv filen är tillgänglig på klustret i */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. I följande kodavsnitt hämta vi först schemat för data som strömmas. Sedan kan skapa vi en strömmande dataframe med hjälp av schemat. Klistra in kodfragmentet i en kodcell och tryck på **SKIFT + RETUR** ska köras.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. Utdata visar schemat för **HVAC.csv**. Den **hvactable** har samma schema också. Utdata visar kolumnerna i tabellen.

    ![Schemat för tabell](./media/apache-spark-connect-to-sql-database/schema-of-table.png "schemat för tabell")

1. Slutligen använder du följande kodavsnitt för att läsa data från HVAC.csv och strömma dem till den **hvactable** i Azure SQL database. Klistra in kodfragmentet i en kodcell, Ersätt platshållarvärdena med värden för Azure SQL-databasen och tryck sedan på **SKIFT + RETUR** ska köras.

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

1. Kontrollera att data strömmas i den **hvactable** genom att köra följande fråga i SQL Server Management Studio (SSMS). Varje gång du kör frågan visar antalet rader i tabellen ökar.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Nästa steg

* [Använd HDInsight Spark-kluster för att analysera data i Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Processen strukturerad direktuppspelning av händelser med hjälp av EventHub](apache-spark-eventhub-structured-streaming.md)
* [Använda Apache Spark Structured Streaming med Apache Kafka på HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
