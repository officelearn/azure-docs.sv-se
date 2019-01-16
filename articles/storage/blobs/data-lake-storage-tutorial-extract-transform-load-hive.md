---
title: 'Självstudier: Utföra ETL-åtgärder (extrahera, transformera, läs in) med hjälp av Apache Hive i Azure HDInsight'
description: I den här självstudien lär du dig hur du extraherar data från en CSV-datauppsättning med rådata, transformerar den med hjälp av Apache Hive i Azure HDInsight och sedan läser in dessa transformerade data till Azure SQL Database med hjälp av Sqoop.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jamesbak
ms.openlocfilehash: 65d2d69c788a54371664d1a443a79bd121332470
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105160"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Självstudier: Extrahera, transformera och läsa in data med hjälp av Apache Hive i Azure HDInsight

I den här självstudien ska du utföra en ETL-åtgärd: extrahera, transformera och läsa in data. Du använder en CSV-datafil med rådata, importerar den till ett Azure HDInsight-kluster, transformerar den med Apache Hive och läser in den till en Azure SQL-databas med Apache Sqoop.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Extrahera och ladda upp data till ett HDInsight-kluster.
> * Transformera data med hjälp av Apache Hive.
> * Läs in data till en Azure SQL-databas med hjälp av Sqoop.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Ett Linux-baserat Hadoop-kluster i HDInsight**: Information om hur du skapar ett nytt Linux-baserat HDInsight-kluster finns i [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera](./data-lake-storage-quickstart-create-connect-hdi-cluster.md).

* **Azure SQL Database**: Du använder en Azure SQL-databas som måldatalager. Om du inte har någon SQL-databas kan du läsa [Skapa en Azure SQL-databas i Azure-portalen](../../sql-database/sql-database-get-started.md).

* **Azure CLI**: Om du inte har installerat Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **En SSH-klient**: Mer information finns i [Ansluta till HDInsight (Hadoop) med hjälp av SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Stegen i den här artikeln kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med Azure HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="download-the-flight-data"></a>Ladda ned flygdata

I den här självstudien används flygdata från Bureau of Transportation Statistics för att demonstrera hur du utför en ETL-åtgärd. Du måste hämta dessa data för att kunna gå självstudien.

1. Gå till [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

1. Välj följande värden på sidan:

   | Namn | Värde |
   | --- | --- |
   | **Filtrera år** |2013 |
   | **Filtrera period** |Januari |
   | **Fält** |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

1. Rensa alla andra fält.

1. Välj **Ladda ned**. Du får en .zip-fil med de datafält du valde.

## <a name="extract-and-upload-the-data"></a>Extrahera och ladda upp data

I det här avsnittet ska du använda `scp` för att överföra data till ditt HDInsight-kluster.

Öppna en kommandotolk och använd följande kommando för att ladda upp .zip-filen till HDInsight-klustrets huvudnod:

```bash
scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
```

* Ersätt \<FILE_NAME> med namnet på .zip-filen.
* Ersätt \<SSH_USER_NAME> med SSH-inloggningen för HDInsight-klustret.
* Ersätt \<CLUSTER_NAME> med namnet på HDInsight-klustret.

Om du använder ett lösenord för att autentisera din SSH-inloggning uppmanas du att ange lösenordet. 

Om du använder en offentlig nyckel kan du behöva använda `-i`-parametern och ange sökvägen till motsvarande privata nyckel. Till exempel `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

När uppladdningen är klar kan du ansluta till klustret med hjälp av SSH. Öppna kommandotolken och ange följande kommando:

```bash
ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
```

Använd följande kommando för att packa upp .zip-filen:

```bash
unzip <FILE_NAME>.zip
```

Kommandot extraherar en **.csv-fil** som är cirka 60 MB.

Använd följande kommandon för att skapa en katalog och kopiera sedan *.csv*-filen till katalogen:

```bash
hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
```

Använd följande kommando för att skapa Data Lake Storage Gen2-filsystemet.

```bash
hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
```

## <a name="transform-the-data"></a>Transformera data

I det här avsnittet ska du använda Beeline för att köra ett Apache Hive-jobb.

Som en del av Apache Hive-jobbet importerar du data från CSV-filen till en Apache Hive-tabell med namnet **delays**.

Från SSH-frågan som du redan har för HDInsight-klustret använder du följande kommando för att skapa och redigera en ny fil med namnet **flightdelays.hql**:

```bash
nano flightdelays.hql
```

Använd följande text som filens innehåll:

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
 LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

-- Drop the delays table if it exists
DROP TABLE delays;
-- Create the delays table and populate it with data
-- pulled in from the CSV file (via the external table defined previously)
CREATE TABLE delays
LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
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

Du sparar filen genom att trycka på ESC och sedan ange `:x`.

Om du vill starta Hive och köra filen **flightdelays.hql** använder du följande kommando:

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
```

När skriptet __flightdelays.hql__ har körts klart använder du följande kommando för att öppna en interaktiv Beeline-session:

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
```

När du får uppmaningen `jdbc:hive2://localhost:10001/>` ska du använda följande fråga för att hämta data från de importerade flygförseningsdata:

```hiveql
INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
SELECT regexp_replace(origin_city_name, '''', ''),
    avg(weather_delay)
FROM delays
WHERE weather_delay IS NOT NULL
GROUP BY origin_city_name;
```

Frågan returnerar en lista över städer som berörs av förseningar på grund av vädret samt genomsnittlig förseningstid och sparar det till `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Senare läser Sqoop data från den här platsen och exporterar dem till Azure SQL Database.

Om du vill avsluta Beeline skriver du `!quit` vid uppmaningen.

## <a name="create-a-sql-database-table"></a>Skapa en SQL-databastabell

Du behöver namnet på servern från SQL-databasen för den här åtgärden. Slutför stegen nedan för att hitta namnet på servern.

1. Gå till [Azure-portalen](https://portal.azure.com).

1. Välj **SQL-databaser**.

1. Filtrera efter namnet på den databas som du vill använda. Serverns namn finns i kolumnen **Servernamn**.

1. Filtrera efter namnet på den databas som du vill använda. Serverns namn finns i kolumnen **Servernamn**.

    ![Hämta information om Azure SQL-server](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Hämta information om Azure SQL-server")

    Det finns många sätt att ansluta till SQL Database och skapa en tabell. Följande steg använder [FreeTDS](http://www.freetds.org/) från HDInsight-klustret.

För att installera FreeTDS använder du följande kommando från en SSH-anslutning till klustret:

```bash
sudo apt-get --assume-yes install freetds-dev freetds-bin
```

När installationen är klar använder du följande kommando för att ansluta till SQL Database-servern.

* Ersätt \<SERVER_NAME> med SQL Database-servernamnet.
* Ersätt \<ADMIN_LOGIN> med administratörsinloggningen för SQL Database.
* Ersätt \<DATABASE_NAME> med databasnamnet.

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
```

När du uppmanas att göra det anger du lösenordet för SQL Database-administratörsinloggningen.

Du får utdata som liknar följande text:

```
locale is "en_US.UTF-8"
locale charset is "UTF-8"
using default charset "UTF-8"
Default database being set to sqooptest
1>
```

Ange följande instruktion vid `1>`-prompten:

```hiveql
CREATE TABLE [dbo].[delays](
[origin_city_name] [nvarchar](50) NOT NULL,
[weather_delay] float,
CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
([origin_city_name] ASC))
GO
```

När instruktionen `GO` har angivits värderas de föregående instruktionerna.
Frågan skapar en tabell med namnet **delays**, som har ett grupperat index.

Använd följande fråga för att kontrollera att tabellen har skapats:

```hiveql
SELECT * FROM information_schema.tables
GO
```

De utdata som genereras liknar följande text:

```
TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
databaseName       dbo             delays        BASE TABLE
```

Skriv `exit` vid uppmaningen `1>` för att avsluta tsql-verktyget.

## <a name="export-and-load-the-data"></a>Exportera och läsa in data

I föregående avsnitt kopierade du transformerade data på platsen `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. I det här avsnittet använder du Sqoop för att exportera data från `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` till tabellen du skapade i Azure SQL-databasen.

Använd följande kommando för att verifiera att Sqoop kan se din SQL-databas:

```bash
sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
```

Det här kommandot returnerar en lista med databaser, däribland databasen som du skapade **delays**-tabellen i.

Använd följande kommando för att exportera data från tabellen **hivesampletable** till tabellen **delays**:

```bash
sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
```

Sqoop ansluter till databasen som innehåller tabellen **delays** och exporterar data från `/tutorials/flightdelays/output`-katalogen till tabellen **delays**.

När `sqoop`-kommandot avslutas använder du tsql-verktyget för att ansluta till databasen:

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
```

Använd följande instruktioner för att verifiera att data exporterades till tabellen **delays**:

```sql
SELECT * FROM delays
GO
```

Du ska se en lista över data i tabellen. Tabellen innehåller stadens namn och genomsnittlig flygförseningstid för den staden.

Skriv `exit` för att avsluta tsql-verktyget.

## <a name="clean-up-resources"></a>Rensa resurser

Alla resurser som använts i den här självstudien fanns redan. Ingen rensning krävs.

## <a name="next-steps"></a>Nästa steg

Mer information om att arbeta med data i HDInsight finns i följande artikel:

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in data med hjälp av Azure Databricks](./data-lake-storage-use-hdi-cluster.md)
