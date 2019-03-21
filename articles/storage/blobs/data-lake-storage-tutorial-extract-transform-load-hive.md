---
title: 'Självstudier: Utföra ETL-åtgärder (extrahera, transformera, läs in) med hjälp av Apache Hive i Azure HDInsight'
description: I den här självstudien lär du dig hur du extraherar data från en CSV-datauppsättning med rådata, transformerar den med hjälp av Apache Hive i Azure HDInsight och sedan läser in dessa transformerade data till Azure SQL Database med hjälp av Sqoop.
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jamesbak
ms.openlocfilehash: cdb89b552d0e328e6685e2bd62ea135e15e4b074
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013445"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Självstudier: Extrahera, transformera och läsa in data med hjälp av Apache Hive i Azure HDInsight

I den här självstudien ska du utföra en ETL-åtgärd: extrahera, transformera och läsa in data. Du använder en CSV-datafil med rådata, importerar den till ett Azure HDInsight-kluster, transformerar den med Apache Hive och läser in den till en Azure SQL-databas med Apache Sqoop.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Extrahera och ladda upp data till ett HDInsight-kluster.
> * Transformera data med hjälp av Apache Hive.
> * Läs in data till en Azure SQL-databas med hjälp av Sqoop.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* **Ett Azure Data Lake Storage Gen2-lagringskonto som har konfigurerats för HDInsight**

    Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **Ett Linux-baserat Hadoop-kluster i HDInsight**

    Gå till [Snabbstart: Komma igång med Apache Hadoop och Apache Hive i Azure HDInsight med hjälp av Azure-portalen](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Azure SQL Database**: Du använder en Azure SQL-databas som måldatalager. Om du inte har någon SQL-databas kan du läsa [Skapa en Azure SQL-databas i Azure-portalen](../../sql-database/sql-database-get-started.md).

* **Azure CLI**: Om du inte har installerat Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **En Secure Shell-klient (SSH)**: Mer information finns i [Ansluta till HDInsight (Hadoop) med hjälp av SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Stegen i den här artikeln kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med Azure HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="download-the-flight-data"></a>Ladda ned flygdata

1. Gå till [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Välj följande värden på sidan:

   | Namn | Värde |
   | --- | --- |
   | Filtrera år |2013 |
   | Filtrera period |Januari |
   | Fält |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Rensa alla andra fält.

3. Välj **Ladda ned**. Du får en .zip-fil med de datafält du valde.

## <a name="extract-and-upload-the-data"></a>Extrahera och ladda upp data

I det här avsnittet laddar du upp data till ditt HDInsight-kluster och kopierar sedan dessa data till ditt Data Lake Storage Gen2-konto.

1. Öppna en kommandotolk och använd följande Secure Copy-kommando (Scp) för att ladda upp .zip-filen till HDInsight-klustrets huvudnod:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Ersätt platshållaren `<file-name>` med namnet på .zip-filen.
   * Ersätt platshållaren `<ssh-user-name>` med SSH-inloggningen för HDInsight-klustret.
   * Ersätt platshållaren `<cluster-name>` med namnet på HDInsight-klustret.

   Om du använder ett lösenord för att autentisera din SSH-inloggning uppmanas du att ange lösenordet.

   Om du använder en offentlig nyckel kan du behöva använda `-i`-parametern och ange sökvägen till motsvarande privata nyckel. Till exempel `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. När uppladdningen är klar kan du ansluta till klustret med hjälp av SSH. Öppna kommandotolken och ange följande kommando:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Använd följande kommando för att packa upp .zip-filen:

   ```bash
   unzip <file-name>.zip
   ```

   Kommandot extraherar en **.csv**-fil.

4. Använd följande kommando för att skapa Data Lake Storage Gen2-filsystemet.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Ersätt platshållaren `<file-system-name>` med det namn som du vill ge filsystemet.

   Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

5. Använd följande kommando för att skapa en katalog.

   ```bash
   hdfs dfs -mkdir -p abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Använd följande kommando för att kopiera *.csv*-filen till katalogen:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Använd citattecken runt filnamnet om filnamnet innehåller blanksteg eller specialtecken.

## <a name="transform-the-data"></a>Transformera data

I det här avsnittet ska du använda Beeline för att köra ett Apache Hive-jobb.

Som en del av Apache Hive-jobbet importerar du data från CSV-filen till en Apache Hive-tabell med namnet **delays**.

1. Från den SSH-prompt som du redan har för HDInsight-klustret använder du följande kommando för att skapa och redigera en ny fil med namnet **flightdelays.hql**:

   ```bash
   nano flightdelays.hql
   ```

2. Ändra följande text genom att ersätta platshållarna `<file-system-name>` och `<storage-account-name>` med namnen på ditt filsystem och lagringskonto. Kopiera och klistra sedan in texten i nano-konsolen genom att trycka på Skift-tangenten tillsammans med höger musknapp.

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Spara filen med hjälp av CTRL+X och skriv sedan `Y` när du tillfrågas.

4. Om du vill starta Hive och köra filen **flightdelays.hql** använder du följande kommando:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. När skriptet __flightdelays.hql__ har körts klart använder du följande kommando för att öppna en interaktiv Beeline-session:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
   ```

6. När du får uppmaningen `jdbc:hive2://localhost:10001/>` ska du använda följande fråga för att hämta data från de importerade flygförseningsdata:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

   Frågan returnerar en lista över städer som berörs av förseningar på grund av vädret samt genomsnittlig förseningstid och sparar det till `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Senare läser Sqoop data från den här platsen och exporterar dem till Azure SQL Database.

7. Om du vill avsluta Beeline skriver du `!quit` vid uppmaningen.

## <a name="create-a-sql-database-table"></a>Skapa en SQL-databastabell

Du behöver namnet på servern från SQL-databasen för den här åtgärden. Slutför stegen nedan för att hitta namnet på servern.

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Välj **SQL-databaser**.

3. Filtrera efter namnet på den databas som du vill använda. Serverns namn finns i kolumnen **Servernamn**.

4. Filtrera efter namnet på den databas som du vill använda. Serverns namn finns i kolumnen **Servernamn**.

    ![Hämta information om Azure SQL-server](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Hämta information om Azure SQL-server")

    Det finns många sätt att ansluta till SQL Database och skapa en tabell. Följande steg använder [FreeTDS](http://www.freetds.org/) från HDInsight-klustret.

5. För att installera FreeTDS använder du följande kommando från en SSH-anslutning till klustret:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. När installationen är klar använder du följande kommando för att ansluta till SQL Database-servern.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Ersätt platshållaren `<server-name>` med lösenordet för namnet på SQL-databasservern.

   * Ersätt platshållaren `<admin-login>` med administratörsinloggningen för SQL Database.

   * Ersätt platshållaren `<database-name>` med databasnamnet

   När du uppmanas att göra det anger du lösenordet för SQL Database-administratörsinloggningen.

   Du får utdata som liknar följande text:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Ange följande instruktion vid `1>`-prompten:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. När instruktionen `GO` har angivits värderas de föregående instruktionerna.

   Frågan skapar en tabell med namnet **delays**, som har ett grupperat index.

9. Använd följande fråga för att kontrollera att tabellen har skapats:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   De utdata som genereras liknar följande text:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Skriv `exit` vid uppmaningen `1>` för att avsluta tsql-verktyget.

## <a name="export-and-load-the-data"></a>Exportera och läsa in data

I föregående avsnitt kopierade du transformerade data på platsen `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. I det här avsnittet använder du Sqoop för att exportera data från `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` till tabellen du skapade i Azure SQL-databasen.

1. Använd följande kommando för att verifiera att Sqoop kan se din SQL-databas:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   Det här kommandot returnerar en lista med databaser, däribland databasen som du skapade **delays**-tabellen i.

2. Använd följande kommando för att exportera data från tabellen **hivesampletable** till tabellen **delays**:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<file-system-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop ansluter till databasen som innehåller tabellen **delays** och exporterar data från `/tutorials/flightdelays/output`-katalogen till tabellen **delays**.

3. När `sqoop`-kommandot avslutas använder du tsql-verktyget för att ansluta till databasen:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Använd följande instruktioner för att verifiera att data exporterades till tabellen **delays**:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Du ska se en lista över data i tabellen. Tabellen innehåller stadens namn och genomsnittlig flygförseningstid för den staden.

5. Skriv `exit` för att avsluta tsql-verktyget.

## <a name="clean-up-resources"></a>Rensa resurser

Alla resurser som använts i den här självstudien fanns redan. Ingen rensning krävs.

## <a name="next-steps"></a>Nästa steg

Mer information om att arbeta med data i HDInsight finns i följande artikel:

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in data med hjälp av Azure Databricks](./data-lake-storage-use-hdi-cluster.md)
