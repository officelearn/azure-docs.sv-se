---
title: "Analysera svarta fördröjning data med Hive i HDInsight - Azure | Microsoft Docs"
description: "Lär dig hur du använder Hive för att analysera data rör sig på Linux-baserade HDInsight och sedan exportera data till SQL-databas med hjälp av Sqoop."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: b2eca1ab7eff006311269c78b1e507cb1417fcc6
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Analysera svarta fördröjning data med hjälp av Hive på Linux-baserat HDInsight

Lär dig hur du analyserar svarta fördröjning data med hjälp av Hive på Linux-baserade HDInsight och hur du exporterar data till Azure SQL Database med hjälp av Sqoop.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystem som används på Azure HDInsight version 3.4 eller senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Förutsättningar

* **Ett HDInsight-kluster**. Se [komma igång med Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) stegvisa instruktioner för hur du skapar ett nytt Linux-baserat HDInsight-kluster.

* **Azure SQL Database**. Du kan använda en Azure SQL database som ett dataarkiv som mål. Om du inte har en SQL-databas, se [skapa en Azure SQL database i Azure portal](../sql-database/sql-database-get-started.md).

* **Azure CLI**. Om du inte har installerat Azure CLI, se [installera Azure CLI 1.0](../cli-install-nodejs.md) fler steg.

## <a name="download-the-flight-data"></a>Ladda ned data rör sig

1. Bläddra till [forskning och innovativa tekniken Administration, som administreras av transport statistik][rita-website].

2. På sidan Välj följande värden:

   | Namn | Värde |
   | --- | --- |
   | Filtrera år |2013 |
   | Filtrera Period |Januari |
   | Fält |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Avmarkera alla andra fält. 

3. Välj **hämta**.

## <a name="upload-the-data"></a>Överföra data

1. Använd följande kommando för att ladda upp ZIP-filen till HDInsight-klustrets huvudnod:

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Ersätt *FILENAME* med namnet på .zip-filen. Ersätt *användarnamn* med SSH-inloggning för HDInsight-kluster. Ersätt *KLUSTERNAMN* med namnet på HDInsight-klustret.

   > [!NOTE]
   > Om du använder ett lösenord för att autentisera SSH-inloggning kan uppmanas du lösenordet. Om du använder en offentlig nyckel kan du behöva använda de `-i` parametern och ange sökvägen till motsvarande privata nyckel. Till exempel `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. När överföringen är klar kan du ansluta till klustret med hjälp av SSH:

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Mer information finns i [Ansluta till HDInsight (Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Använd följande kommando för att packa upp ZIP-filen:

    ```
    unzip FILENAME.zip
    ```

    Det här kommandot extraheras en CSV-fil som är ungefär 60 MB.

4. Använder du följande kommando för att skapa en katalog på HDInsight lagring och kopiera filen till katalogen:

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Skapa och köra HiveQL

Använd följande steg för att importera data från CSV-filen till en Hive-tabell med namnet **fördröjningar**.

1. Använd följande kommando för att skapa och redigera en ny fil med namnet **flightdelays.hql**:

    ```
    nano flightdelays.hql
    ```

    Använd följande text som innehållet i den här filen:

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

2. Använd Ctrl + X, då Y om du vill spara filen.

3. Att starta Hive och köra den **flightdelays.hql** fil, använder du följande kommando:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Efter den __flightdelays.hql__ skriptet har slutförts körs, Använd följande kommando för att öppna en interaktiv session Beeline:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. När du får den `jdbc:hive2://localhost:10001/>` uppmanar, Använd följande fråga för att hämta data från importerade svarta fördröjning data:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Den här frågan returnerar en lista över orter som erfarna väder fördröjningar, samt den genomsnittliga fördröjningen och sparar `/tutorials/flightdelays/output`. Senare, Sqoop läser data från den här platsen och exporterar den till Azure SQL Database.

6. Om du vill avsluta Beeline ange `!quit` i Kommandotolken.

## <a name="create-a-sql-database"></a>Skapa en SQL-databas

Om du redan har en SQL-databas måste du hämta namnet på servern. Att hitta servernamnet i den [Azure-portalen](https://portal.azure.com)väljer **SQL-databaser**, och filtrera sedan på namnet på databasen som du vill använda. Namnet på server som ingår i den **SERVER** kolumn.

Om du inte redan har en SQL-databas kan använda informationen i [skapa en Azure SQL database i Azure portal](../sql-database/sql-database-get-started.md) att skapa en. Spara servernamnet på som används för databasen.

## <a name="create-a-sql-database-table"></a>Skapa en SQL-databastabell

> [!NOTE]
> Det finns många sätt att ansluta till SQL-databas och skapa en tabell. Följande steg används [FreeTDS](http://www.freetds.org/) från HDInsight-klustret.


1. Använd följande kommando från en SSH-anslutning till klustret för att installera FreeTDS:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. När installationen är klar använder du följande kommando för att ansluta till SQL Database-server. Ersätt **serverName** med SQL Database-servernamn. Ersätt **adminLogin** och **adminPassword** med inloggningen för SQL-databas. Ersätt **databaseName** med namnet på databasen.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    När du uppmanas, ange lösenordet för administratörsinloggning SQL-databas.

    Visas utdata som liknar följande:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. På den `1>` uppmanar, ange följande rader:

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    När den `GO` uttryck har angetts, tidigare rapporter utvärderas. Den här frågan skapar en tabell med namnet **fördröjningar**, med ett grupperat index.

    Använd följande fråga för att kontrollera att tabellen har skapats:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    De utdata som genereras liknar följande text:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Ange `exit` på den `1>` prompten för att avsluta tsql-verktyget.

## <a name="export-data-with-sqoop"></a>Exportera data med Sqoop

1. Använd följande kommando för att kontrollera att Sqoop kan se din SQL-databas:

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Det här kommandot returnerar en lista över databaser, inklusive den databas som du skapade tabellen fördröjningar tidigare.

2. Använd följande kommando för att exportera data från hivesampletable till tabellen fördröjningar:

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop ansluter till databasen som innehåller tabellen fördröjningar och exporterar data från den `/tutorials/flightdelays/output` katalogen till tabellen fördröjningar.

3. När kommandot sqoop har slutförts kan du använda verktyget tsql för att ansluta till databasen:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Använda följande instruktioner för att verifiera att data har exporterats till tabellen fördröjningar:

    ```
    SELECT * FROM delays
    GO
    ```

    Du bör se en lista över data i tabellen. Typen `exit` avsluta tsql-verktyget.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig fler sätt att arbeta med data i HDInsight finns i följande artiklar:

* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Oozie med HDInsight][hdinsight-use-oozie]
* [Använda Sqoop med HDInsight][hdinsight-use-sqoop]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Utveckla Java-MapReduce-program för Hadoop i HDInsight][hdinsight-develop-mapreduce]
* [Utveckla Python strömning MapReduce program för HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
