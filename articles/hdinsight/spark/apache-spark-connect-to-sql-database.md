---
title: Använda Apache Spark att läsa och skriva data till Azure SQL database | Microsoft Docs
description: Lär dig hur du konfigurerar en anslutning mellan HDInsight Spark-kluster och en Azure SQL database om du vill läsa data att skriva data och dataströmmen data till en SQL-databas
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: nitinme
ms.openlocfilehash: 6ef0b1ce589bd19693d45a9e4f579ef260530a40
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Använda HDInsight Spark-klustret för att läsa och skriva data till Azure SQL-databas

Lär dig hur du ansluter ett Apache Spark-kluster i Azure HDInsight med en Azure SQL database och sedan läsa, skriva och strömma data till SQL-databasen. Anvisningarna i den här artikeln använder en Jupyter-anteckningsbok för att köra Scala kodavsnitt. Du kan dock skapa ett fristående program i Scala eller Python och utföra samma åtgärder. 

## <a name="prerequisites"></a>Förutsättningar

* **Azure HDInsight Spark-kluster**.  Följ anvisningarna på [skapar ett Apache Spark-kluster i HDInsight](apache-spark-jupyter-spark-sql.md).

* **Azure SQL database**. Följ anvisningarna på [skapa en Azure SQL database](../../sql-database/sql-database-get-started-portal.md). Kontrollera att du skapar en databas med exemplet **AdventureWorksLT** schema och data. Kontrollera också att du skapar en brandväggsregel på servernivå så att din klients IP-adress för åtkomst till SQL-databasen på servern. Instruktionerna för att lägga till brandväggsregeln är tillgänglig i samma artikel. När du har skapat din Azure SQL-databas, se till följande värden praktiska. Du måste kunna ansluta till databasen från ett Spark-kluster.

    * Servernamnet som värd för Azure SQL-databas
    * Azure SQL-databasnamn
    * Azure SQL database admin användarnamn / lösenord

* **SQL Server Management Studio**. Följ anvisningarna på [Använd SSMS att ansluta och fråga efter data](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok

Börja med att skapa en Jupyter-anteckningsbok som är associerade med Spark-klustret. Du kan använda den här anteckningsboken för att köra kodstycken som används i denna artikel. 

1. Från den [Azure-portalen](https://portal.azure.com/), öppna ditt kluster. 

2. Från den **snabblänkar** klickar du på **kluster instrumentpaneler** att öppna den **kluster instrumentpaneler** vyn.  Om du inte ser **snabblänkar**, klickar du på **översikt** i den vänstra menyn på bladet.

    ![Instrumentpanelen i klustret på Spark](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "instrumentpanelen i klustret på Spark") 

3. Klicka på **Jupyter-anteckningsbok**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    ![Jupyter-anteckningsbok på Spark](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "Jupyter notebook i Spark")
   
   > [!NOTE]
   > Du kan också komma åt Jupyter notebook i Spark-kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

4. I Jupyter-anteckningsbok från det övre högra hörnet, klickar du på **ny**, och klicka sedan på **Spark** att skapa en Scala bärbar dator. Jupyter-anteckningsböcker på HDInsight Spark-kluster även ange den **PySpark** kernel för Python2 program och **PySpark3** kernel för Python3 program. Den här artikeln skapar vi en Scala bärbar dator.
   
    ![Kärnor för Jupyter notebook i Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "kärnor för Jupyter notebook i Spark")

    Mer information om dessa kernlar finns i [Använda kernlar i Jupyter-anteckningsböcker med Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]
   > I den här artikeln använder vi en Spark (Scala) kernel eftersom strömmande data från Spark i SQL-databas stöds endast för närvarande i Scala och Java. Trots att läsa från och skriva till SQL kan göras med Python, konsekvensen i den här artikeln kan vi använda Scala för alla tre åtgärder.
   >

5. Då öppnas en ny anteckningsbok med ett standardnamn **Namnlös**. Klicka på anteckningsbokens namn och ange ett valfritt namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för anteckningsboken")

Du kan nu börja skapa ditt program.
    
## <a name="read-data-from-azure-sql-database"></a>Läsa data från Azure SQL-databas

I det här avsnittet kan du läsa data från en tabell (till exempel **SalesLT.Address**) som finns i databasen AdventureWorks.

1. Klistra in följande kodutdrag i en ny Jupyter-anteckningsbok i en cell i koden, och ersätta platshållarvärdena med värden för din Azure SQL-databas.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

2. Följande kodavsnitt bygger en JDBC-URL som du kan överföra till Spark-dataframe API: er skapas en `Properties` objekt som innehåller parametrar. Klistra sammandraget i en kod cell och tryck på **SKIFT + RETUR** ska köras.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

3. Följande kodutdrag skapar en dataframe med data från en tabell i Azure SQL database. I det här kodstycket vi använder en **SalesLT.Address** tabell som är tillgängliga som en del av den **AdventureWorksLT** databas. Klistra sammandraget i en kod cell och tryck på **SKIFT + RETUR** ska köras.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

4. Du kan nu utföra åtgärder på dataframe, till exempel komma dataschemat:

       sqlTableDF.printSchema
   
    Du kan se utdata som liknar följande:

    ![Ange ett namn för anteckningsboken](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Ange ett namn för anteckningsboken")

5. Du kan också utföra åtgärder som hämtar de översta 10 raderna.

       sqlTableDF.show(10)

6. Eller hämta specifika kolumner i DataSet.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Skriva data till Azure SQL-databas

I det här avsnittet använder vi en CSV-exempelfil tillgängliga i klustret att skapa en tabell i Azure SQL-databas och fylla det med data. Exempel-CSV-filen (**HVAC.csv**) är tillgängligt på alla HDInsight-kluster på `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Klistra in följande kodutdrag i en ny Jupyter-anteckningsbok i en cell i koden, och ersätta platshållarvärdena med värden för din Azure SQL-databas.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

2. Följande kodavsnitt bygger en JDBC-URL som du kan överföra till Spark-dataframe API: er skapas en `Properties` objekt som innehåller parametrar. Klistra sammandraget i en kod cell och tryck på **SKIFT + RETUR** ska köras.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

3. Följande kodavsnitt extraherar schemat för data i HVAC.csv och använder schemat för att läsa in data från CSV i ett dataframe `readDf`. Klistra sammandraget i en kod cell och tryck på **SKIFT + RETUR** ska köras.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

4. Använd den `readDf` dataframe att skapa en tillfällig tabell `temphvactable`. Sedan använda den temporära tabellen för att skapa en hive-tabell `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

5. Slutligen använda hive-tabell för att skapa en tabell i Azure SQL-databas. Följande kodutdrag skapar `hvactable` i Azure SQL-databas.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

6. Ansluta till Azure SQL-databas med hjälp av SSMS och kontrollera att du ser en `dbo.hvactable` det.

    a. Starta SSMS och Anslut till Azure SQL-databasen genom att ange anslutningsinformation som visas i skärmbilden nedan.

    ![Anslut till SQL database med SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Anslut till SQL database med SSMS")

    b. Från Object Explorer, expandera Azure SQL-databasen och noden tabell om du vill se den **dbo.hvactable** skapas.

    ![Anslut till SQL database med SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Anslut till SQL database med SSMS")

## <a name="stream-data-into-azure-sql-database"></a>Dataströmmen data till Azure SQL-databas

I det här avsnittet vi strömma data till den **hvactable** att du redan har skapats i Azure SQL database i föregående avsnitt.

1. Som ett första steg bör du kontrollera att det finns inga poster i den **hvactable**. Med hjälp av SSMS, kör följande fråga på tabellen.

       DELETE FROM [dbo].[hvactable]

2. Skapa en ny Jupyter-anteckningsbok i HDInsight Spark-klustret. Klistra in följande kodavsnitt i en cell i koden, och tryck sedan på **SKIFT + RETUR**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

3. Vi strömma data från den **HVAC.csv** till hvactable. HVAC.csv-filen är tillgänglig på klustret i */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. I följande fragment hämta vi först schemat för data som strömmas. Sedan skapar vi en strömmande dataframe med schemat. Klistra sammandraget i en kod cell och tryck på **SKIFT + RETUR** ska köras.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema1).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

4. Utdata visar schemat för **HVAC.csv**. Den **hvactable** har det samma scheman. Utdata visar kolumnerna i tabellen.

    ![Schemat för tabell](./media/apache-spark-connect-to-sql-database/schema-of-table.png "schemat för tabell")

5. Slutligen använder du följande kodavsnitt att läsa data från HVAC.csv och strömma till den **hvactable** i Azure SQL-databas. Klistra sammandraget i en cell i koden, ersätta platshållarvärdena med värden för din Azure SQL-databas och tryck sedan på **SKIFT + RETUR** ska köras.

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

6. Kontrollera att data som strömmas till den **hvactable** genom att köra följande fråga i SQL Server Management Studio (SSMS). Varje gång du kör frågan, den visar antalet rader i tabellen stigande.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Nästa steg

* [Använda HDInsight Spark-klustret för att analysera data i Data Lake Store](apache-spark-use-with-data-lake-store.md)
* [Processen strukturerad strömmande händelser med hjälp av EventHub](apache-spark-eventhub-structured-streaming.md)
* [Använda Spark strukturerad strömning med Kafka på HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
