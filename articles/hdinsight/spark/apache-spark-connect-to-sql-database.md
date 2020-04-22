---
title: Använda Apache Spark för att läsa och skriva data till Azure SQL Database
description: Lär dig hur du konfigurerar en anslutning mellan HDInsight Spark-kluster och en Azure SQL-databas. Så här läser du data, skriver data och strömmar data till en SQL-databas
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 4e783a233bd35e012c02fbbbdc7f4223552fc734
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686854"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Använda HDInsight Spark-kluster för att läsa och skriva data till Azure SQL Database

Lär dig hur du ansluter ett Apache Spark-kluster i Azure HDInsight med en Azure SQL-databas. Läs, skriva och strömma data till SQL-databasen. Instruktionerna i den här artikeln använder en Jupyter Notebook för att köra Scala-kodavsnitten. Du kan dock skapa ett fristående program i Scala eller Python och utföra samma uppgifter.

## <a name="prerequisites"></a>Krav

* Azure HDInsight Spark-kluster.  Följ instruktionerna på [Skapa ett Apache Spark-kluster i HDInsight](apache-spark-jupyter-spark-sql.md).

* Azure SQL Database. Följ instruktionerna på [Skapa en Azure SQL Database](../../sql-database/sql-database-get-started-portal.md). Se till att du skapar en databas med exempel på **AdventureWorksLT-schema** och data. Se också till att du skapar en brandväggsregel på servernivå så att klientens IP-adress kan komma åt SQL-databasen på servern. Instruktionerna för att lägga till brandväggsregeln finns i samma artikel. När du har skapat din Azure SQL-databas kontrollerar du att du har följande värden till hands. Du behöver dem för att ansluta till databasen från ett Spark-kluster.

    * Servernamn som är värd för Azure SQL Database.
    * Namn på Azure SQL-databas.
    * Azure SQL Database admin användarnamn / lösenord.

* SQL Server Management Studio (SSMS). Följ instruktionerna på [Använd SSMS för att ansluta och fråga data](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter Notebook

Börja med att skapa en Jupyter-anteckningsbok som är associerad med Spark-klustret. Du kan använda den här anteckningsboken för att köra kodavsnitten som används i den här artikeln.

1. Öppna klustret från [Azure-portalen.](https://portal.azure.com/)
1. Välj **Jupyter-anteckningsbok** under **Klusterinstrumentpaneler** till höger.  Om du inte ser **klusterinstrumentpaneler**väljer du **Översikt** på den vänstra menyn. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    ![Jupyter anteckningsbok på Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter anteckningsbok på Spark")

   > [!NOTE]  
   > Du kan också komma åt Jupyter-anteckningsboken i Spark-klustret genom att öppna följande WEBBADRESS i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Klicka på **Nytt**i den jupyterska anteckningsboken i jupyter och klicka sedan på **Spark** för att skapa en Scala-anteckningsbok. Jupyter-bärbara datorer i HDInsight Spark-klustret tillhandahåller också **PySpark-kärnan** för Python2-program och **PySpark3-kärnan** för Python3-program. I den här artikeln skapar vi en Scala-anteckningsbok.

    ![Kärnor för Jupyter notebook på Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kärnor för Jupyter notebook på Spark")

    Mer information om dessa kernlar finns i [Använda kernlar i Jupyter-anteckningsböcker med Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > I den här artikeln använder vi en Spark -kärna (Scala), eftersom strömmande data från Spark till SQL-databas för närvarande bara stöds i Scala och Java. Även om läsning från och skrivning i SQL kan göras med Python, för konsekvens i den här artikeln, använder vi Scala för alla tre åtgärder.

1. En ny anteckningsbok öppnas med ett standardnamn, **Untitled**. Klicka på anteckningsbokens namn och ange ett namn som du väljer.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för anteckningsboken")

Nu kan du börja skapa ditt program.

## <a name="read-data-from-azure-sql-database"></a>Läsa data från Azure SQL Database

I det här avsnittet läser du data från en tabell (till exempel **SalesLT.Address)** som finns i AdventureWorks-databasen.

1. I en ny Jupyter-anteckningsbok, i en kodcell, klistrar du in följande kodavsnitt och ersätter platshållarvärdena med värdena för din Azure SQL-databas.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

1. Använd kodavsnittet nedan för att skapa en JDBC-URL som du kan skicka till Spark-dataram-API:erna. Koden skapar ett `Properties` objekt som ska innehålla parametrarna. Klistra in kodavsnittet i en kodcell och tryck på **SKIFT + RETUR** för att köras.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Använd kodavsnittet nedan för att skapa en dataram med data från en tabell i din Azure SQL-databas. I det här kodavsnittet `SalesLT.Address` använder vi en tabell som är tillgänglig som en del av **AdventureWorksLT-databasen.** Klistra in kodavsnittet i en kodcell och tryck på **SKIFT + RETUR** för att köras.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Du kan nu utföra åtgärder på dataramen, till exempel hämta dataschemat:

    ```scala
    sqlTableDF.printSchema
    ```

    Du ser en utdata som liknar följande bild:

    ![schemautdata](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "schemautdata")

1. Du kan också göra åtgärder som, hämta de 10 översta raderna.

    ```scala
    sqlTableDF.show(10)
    ```

1. Hämta eller hämta specifika kolumner från datauppsättningen.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Skriva data till Azure SQL Database

I det här avsnittet använder vi en EXEMPEL-CSV-fil som är tillgänglig i klustret för att skapa en tabell i Azure SQL Database och fylla den med data. Exempel-CSV-filen **(HVAC.csv)** är tillgänglig på alla `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`HDInsight-kluster på .

1. I en ny Jupyter-anteckningsbok, i en kodcell, klistrar du in följande kodavsnitt och ersätter platshållarvärdena med värdena för din Azure SQL-databas.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

1. Följande kodavsnitt skapar en JDBC-URL som du kan skicka till Spark-dataram-API:erna. Koden skapar ett `Properties` objekt som ska innehålla parametrarna. Klistra in kodavsnittet i en kodcell och tryck på **SKIFT + RETUR** för att köras.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Använd följande kodavsnitt för att extrahera schemat för data i HVAC.csv och använda schemat för att `readDf`läsa in data från CSV i en dataram, . Klistra in kodavsnittet i en kodcell och tryck på **SKIFT + RETUR** för att köras.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Använd `readDf` dataramen för att skapa `temphvactable`en temporär tabell. Använd sedan den temporära tabellen `hvactable_hive`för att skapa en registreringstabell.

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Slutligen använder du registreringsfilen för att skapa en tabell i Azure SQL Database. Följande kodavsnitt skapas `hvactable` i Azure SQL Database.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Anslut till Azure SQL Database med SSMS `dbo.hvactable` och kontrollera att du ser en där.

    a. Starta SSMS och anslut till Azure SQL Database genom att tillhandahålla anslutningsinformation som visas i skärmbilden nedan.

    ![Ansluta till SQL-databas med SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Ansluta till SQL-databas med SSMS1")

    b. Från **Object Explorer**expanderar du Azure SQL Database och tabellnoden för att se **dbo.hvactable** som skapats.

    ![Ansluta till SQL-databas med SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Ansluta till SQL-databas med SSMS2")

1. Kör en fråga i SSMS för att visa kolumnerna i tabellen.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Strömma data till Azure SQL Database

I det här avsnittet skickar `hvactable` vi data till det som du redan har skapat i Azure SQL Database i föregående avsnitt.

1. Som ett första steg kontrollerar du `hvactable`att det inte finns några poster i . Kör följande fråga i tabellen med SSMS.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Skapa en ny Jupyter-anteckningsbok i HDInsight Spark-klustret. Klistra in följande kodavsnitt i en kodcell och tryck sedan på **SKIFT + RETUR:**

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Vi strömma data från **HVAC.csv** i `hvactable`. HVAC.csv-filen finns i `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`klustret på . I följande kodavsnitt får vi först schemat för de data som ska strömmas. Sedan skapar vi en strömmande dataram med det schemat. Klistra in kodavsnittet i en kodcell och tryck på **SKIFT + RETUR** för att köras.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. Utdata visar schemat för **HVAC.csv**. Den `hvactable` har samma schema också. Utdata visar kolumnerna i tabellen.

    !['hdinsight Apache Spark schematabell'](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schema för tabellen")

1. Slutligen använder du följande kodavsnitt för att läsa data från HVAC.csv och strömma det `hvactable` till i Azure SQL Database. Klistra in kodavsnittet i en kodcell, ersätt platshållarvärdena med värdena för Din Azure SQL-databas och tryck sedan på **SKIFT + RETUR** för att köra.

    ```scala
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
    ```

1. Kontrollera att data strömmas till `hvactable` genom att köra följande fråga i SQL Server Management Studio (SSMS). Varje gång du kör frågan visas antalet rader i tabellen som ökar.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Nästa steg

* [Använda HDInsight Spark-kluster för att analysera data i DataSjölagring](apache-spark-use-with-data-lake-store.md)
* [Läsa in data och köra frågor på ett Apache Spark-kluster i Azure HDInsight](apache-spark-load-data-run-query.md)
* [Använda Apache Spark Structured Streaming med Apache Kafka i HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
