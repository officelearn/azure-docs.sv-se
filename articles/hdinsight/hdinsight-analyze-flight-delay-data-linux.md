---
title: 'Självstudie: Utföra åtgärder för att extrahera, transformera, läsa in (ETL) med Hive i HDInsight – Azure '
description: Självstudie – Lär dig att extrahera data från en rå CSV-datauppsättning, omvandla dem med Hive på HDInsight och sedan läsa in transformerade data i Azure SQL-databas med hjälp av Apache Sqoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/15/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: 6eae1a64b6eba54fd35f8371a11e7b5bfeaac2ab
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296535"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-in-azure-hdinsight"></a>Självstudier: Extrahera, transformera och läsa in data med Apache Hive i Azure HDInsight

I den här självstudien får du ta en rå CSV-datafilen offentligt tillgängliga flygning data importera den till en lagringsplats för HDInsight-kluster och omvandlar data med [Apache Hive](https://hive.apache.org/) i Azure HDInsight. När dessa data har transformerats läser du in dem till en Azure SQL-databas med hjälp av [Apache Sqoop](https://sqoop.apache.org/).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Ladda ned exempelflygdata
> * Ladda upp data till ett HDInsight-kluster
> * Transformera data med Hive
> * Skapa en tabell i en Azure SQL database
> * Använd Sqoop för att exportera data till en Azure SQL database

Följande bild visar ett typiskt ETL-programflöde.

![ETL-åtgärd med Apache Hive på Azure HDInsight](./media/hdinsight-analyze-flight-delay-data-linux/hdinsight-etl-architecture.png "ETL-åtgärd med Apache Hive på Azure HDInsight")

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Apache Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* En Azure SQL-databas. Du använder en Azure SQL-databas som måldatalager. Om du inte har någon SQL-databas kan du läsa [Skapa en Azure SQL-databas i Azure-portalen](../sql-database/sql-database-single-database-get-started.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Ladda ned flygdata

1. Gå till [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Rensa alla fält på sidan och välj sedan följande värden:

   | Namn | Värde |
   | --- | --- |
   | Filtrera år |2019 |
   | Filtrera period |Januari |
   | Fält |År, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, ursprung, OriginCityName, OriginState, DestAirportID, mål, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

3. Välj **Ladda ned**. Du får en .zip-fil med de datafält du valde.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Ladda upp data till ett HDInsight-kluster

Det finns många sätt att överföra data till lagring som är associerade med ett HDInsight-kluster. I det här avsnittet använder du `scp` för att ladda upp data. Om du vill veta mer om andra sätt att ladda upp data kan du läsa [Överföra data till HDInsight](hdinsight-upload-data.md).

1. Ladda upp ZIP-filen till HDInsight-klustrets huvudnod. Redigera kommandot nedan genom att ersätta `FILENAME` med namnet på .zip-filen och `CLUSTERNAME` med namnet på HDInsight-klustret. Öppna en kommandotolk, ange din arbetskatalog till filens plats och ange sedan kommandot.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

2. När uppladdningen är klar kan du ansluta till klustret med hjälp av SSH. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på HDInsight-klustret. Ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Ställa in miljövariabeln när en SSH-anslutning har upprättats. Ersätt `FILE_NAME`, `SQL_SERVERNAME`, `SQL_DATABASE`, `SQL_USER`, och `SQL_PASWORD` med lämpliga värden. Ange sedan kommando:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Packa upp .zip-filen genom att ange kommandot nedan:

    ```bash
    unzip $FILENAME.zip
    ```

5. Skapa en katalog på HDInsight storage och sedan kopiera CSV-filen till katalogen genom att ange kommandot nedan:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformera data med en Hive-fråga

Det finns många sätt att köra ett Hive-jobb på ett HDInsight-kluster. I det här avsnittet använder du [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) för att köra ett Hive-jobb. Information om andra metoder för att köra ett Hive-jobb finns i avsnittet om att [använda Apache Hive i HDInsight](./hadoop/hdinsight-use-hive.md).

Som en del av Hive-jobbet importerar du data från .csv-filen till en Hive-tabell med namnet **Delays** (Fördröjningar).

1. Från SSH-prompten som du redan har för HDInsight-kluster, Använd följande kommando för att skapa och redigera en ny fil med namnet **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Använd följande text som filens innehåll:

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
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
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

3. Om du vill spara filen, trycker du på **Ctrl + X**, sedan **y**, ange sedan.

4. Om du vill starta Hive och köra filen **flightdelays.hql** använder du följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. När skriptet **flightdelays.hql** har körts klart använder du följande kommando för att öppna en interaktiv Beeline-session:

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

    Frågan returnerar en lista över städer som berörs av förseningar på grund av vädret samt genomsnittlig förseningstid och sparar det till `/tutorials/flightdelays/output`. Senare läser Sqoop data från den här platsen och exporterar dem till Azure SQL Database.

7. Om du vill avsluta Beeline skriver du `!quit` vid uppmaningen.

## <a name="create-a-sql-database-table"></a>Skapa en SQL-databastabell

Det finns många sätt att ansluta till SQL Database och skapa en tabell. Följande steg använder [FreeTDS](http://www.freetds.org/) från HDInsight-klustret.

1. Om du vill installera FreeTDS, använder du följande kommando från den öppna SSH-anslutningen till klustret:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. När installationen är klar använder du följande kommando för att ansluta till SQL Database-servern.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Du får utdata som liknar följande text:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Vid uppmaningen `1>` anger du följande rader:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    När instruktionen `GO` har angivits värderas de föregående instruktionerna. Den här instruktionen skapar en tabell med namnet **fördröjningar**, med ett grupperat index.

    Använd följande fråga för att verifiera att tabellen har skapats:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    De utdata som genereras liknar följande text:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Skriv `exit` vid uppmaningen `1>` för att avsluta tsql-verktyget.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Exportera data till SQL-databas med hjälp av Apache Sqoop

I föregående avsnitt kopierade du omvandlade data på `/tutorials/flightdelays/output`. I det här avsnittet använder du Sqoop för att exportera data från `/tutorials/flightdelays/output` till tabellen du skapade i Azure SQL Database.

1. Kontrollera att Sqoop kan se din SQL-databas genom att ange kommandot nedan:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Det här kommandot returnerar en lista över databaser, bland annat den databas du skapade den `delays` tabellen tidigare.

2. Exportera data från `/tutorials/flightdelays/output` till den `delays` tabell genom att ange kommandot nedan:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop ansluter till databasen som innehåller den `delays` tabell och exporterar data från den `/tutorials/flightdelays/output` katalogen till den `delays` tabell.

3. När sqoop-kommandot har slutförts, använder du verktyget tsql för att ansluta till databasen genom att ange kommandot nedan:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Använd följande instruktioner för att verifiera att data exporterades till fördröjningstabellen:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Du ska se en lista över data i tabellen. Tabellen innehåller stadens namn och genomsnittlig flygförseningstid för den staden. 

    Skriv `exit` för att avsluta tsql-verktyget.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster, se [ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI](./hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du tog en rå CSV-datafilen, importeras det till en lagringsplats för HDInsight-kluster och sedan omvandlade data med Apache Hive i Azure HDInsight.  Gå vidare till nästa självstudie för att lära dig hur du skapar HDInsight Hadoop-kluster på begäran med hjälp av Azure Data Factory.

> [!div class="nextstepaction"]
>[Skapa Apache Hadoop-kluster på begäran i HDInsight med hjälp av Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md)

Mer information om att arbeta med data i HDInsight finns i följande artiklar:

* [Självstudie: Extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](../storage/blobs/data-lake-storage-tutorial-extract-transform-load-hive.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Utveckla Java MapReduce-program för Apache Hadoop på HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Använda Apache Oozie med HDInsight](hdinsight-use-oozie-linux-mac.md)
* [Använd Apache Sqoop med HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md)