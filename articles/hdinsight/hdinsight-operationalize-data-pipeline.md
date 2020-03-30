---
title: Operationalisera en dataanalyspipeline - Azure
description: Ställ in och kör en exempeldatapipeline som utlöses av nya data och ger koncisa resultat.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 16c7af4d66bd550eb4a286de7c86c436b1fe10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922668"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operationalisera en pipeline för dataanalys

*Datapipellines* ligger till grund för många dataanalyslösningar. Som namnet antyder tar en datapipeline rådata, rensar och omformar den efter behov och utför sedan vanligtvis beräkningar eller aggregeringar innan de bearbetade data lagras. Bearbetade data förbrukas av klienter, rapporter eller API:er. En datapipeline måste ge repeterbara resultat, oavsett om det är enligt ett schema eller när den utlöses av nya data.

I den här artikeln beskrivs hur du kan operationalisera dina datapipelmän för repeterbarhet med hjälp av Oozie som körs på HDInsight Hadoop-kluster. Exempelscenariot går igenom en datapipeline som förbereder och bearbetar flygtidsseriedata.

I följande scenario är indata en platt fil som innehåller en batch med flygdata under en månad. Dessa flygdata innehåller information som ursprung och destinationsflygplats, de flygmil, avgångs- och ankomsttiderna och så vidare. Målet med denna pipeline är att sammanfatta dagliga flygbolag prestanda, där varje flygbolag har en rad för varje dag med den genomsnittliga avgång och ankomst förseningar i minuter, och den totala miles flugit den dagen.

| YEAR | MONTH | DAY_OF_MONTH | Bärare |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | Aa | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | Dl | 6.935409 | -2.1893024 | 1909696 |

Exempelpipelinen väntar tills en ny tidsperiods flygdata anländer och lagrar sedan den detaljerade flyginformationen i ditt Apache Hive-informationslager för långsiktiga analyser. Pipelinen skapar också en mycket mindre datauppsättning som sammanfattar bara de dagliga flygdata. Den här dagliga flygsammanfattningsdata skickas till en SQL-databas för att tillhandahålla rapporter, till exempel för en webbplats.

Följande diagram illustrerar exempelpipelinen.

![Översikt över datarörledning för HDI-flygexempel](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Apache Oozie lösning översikt

Den här pipelinen använder Apache Oozie som körs på ett HDInsight Hadoop-kluster.

Oozie beskriver sina rörledningar i form av *åtgärder,* *arbetsflöden*och *samordnare*. Åtgärder bestämmer det verkliga arbete som ska utföras, till exempel att köra en Hive-fråga. Arbetsflöden definierar sekvensen av åtgärder. Koordinatorer definierar schemat för när arbetsflödet körs. Koordinatorer kan också vänta på tillgängligheten av nya data innan du startar en instans av arbetsflödet.

Följande diagram visar utformningen på hög nivå av det här exemplet Oozie-pipelinen.

![Oozie Flight exempel Data Pipeline](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Etablera Azure-resurser

Den här pipelinen kräver en Azure SQL-databas och ett HDInsight Hadoop-kluster på samma plats. Azure SQL Database lagrar både sammanfattningsdata som produceras av pipelinen och Oozie Metadata Store.

### <a name="provision-azure-sql-database"></a>Etablera Azure SQL-databas

1. Skapa en Azure SQL-databas. Se [Skapa en Azure SQL-databas i Azure-portalen](../sql-database/sql-database-single-database-get-started.md).

1. Om du vill vara säker på att ditt HDInsight-kluster kan komma åt den anslutna Azure SQL-databasen konfigurerar du Azure SQL Database-brandväggsregler så att Azure-tjänster och resurser kan komma åt servern. Du kan aktivera det här alternativet i Azure-portalen genom att välja **Ange serverbrandvägg**och välja **PÅ** under **Tillåt Azure-tjänster och resurser** för åtkomst till den här servern för Azure SQL Database-servern eller-databasen. Mer information finns i [Skapa och hantera IP-brandväggsregler](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. Använd [Frågeredigeraren](../sql-database/sql-database-single-database-get-started.md#query-the-database) för att `dailyflights` köra följande SQL-uttryck för att skapa den tabell som lagrar summerade data från varje körning av pipelinen.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Din Azure SQL-databas är nu klar.

### <a name="provision-an-apache-hadoop-cluster"></a>Etablera ett Apache Hadoop-kluster

Skapa ett Apache Hadoop-kluster med en anpassad metabutik. När klustret skapas från portalen, på fliken **Lagring,** se till att du väljer din SQL-databas under **Metastore-inställningar**. Mer information om hur du väljer ett metalager finns i [Välj ett anpassat metalager när klustret skapas](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Mer information om hur du skapar kluster finns [i Komma igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Verifiera SSH-tunneluppsättning

Om du vill använda Oozie Web Console för att visa status för dina koordinator- och arbetsflödesinstanser konfigurerar du en SSH-tunnel till ditt HDInsight-kluster. Mer information finns i [SSH Tunnel](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Du kan också använda Chrome med [Foxy Proxy-tillägget](https://getfoxyproxy.org/) för att bläddra i klustrets webbresurser i SSH-tunneln. Konfigurera den för att proxy `localhost` alla begäran via värden på tunnelns port 9876. Den här metoden är kompatibel med Windows Subsystem för Linux, även känd som Bash på Windows 10.

1. Kör följande kommando för att öppna en SSH-tunnel till klustret, där `CLUSTERNAME` är namnet på klustret:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kontrollera att tunneln är i drift genom att navigera till Ambari på huvudet noden genom att bläddra till:

    `http://headnodehost:8080`

1. För att komma åt **Oozie Web Console** inifrån Ambari, navigera till **Oozie** > **Snabblänkar** > [Active server] > **Oozie Web UI**.

## <a name="configure-hive"></a>Konfigurera Hive

### <a name="upload-data"></a>Ladda upp data

1. Hämta en CSV-exempelfil som innehåller flygdata i en månad. Ladda ner `2017-01-FlightData.zip` zip-filen från [HDInsight GitHub-databasen](https://github.com/hdinsight/hdinsight-dev-guide) och packa `2017-01-FlightData.csv`upp den till CSV-filen .

1. Kopiera den här CSV-filen till Det Azure Storage-konto som är `/example/data/flights` kopplat till ditt HDInsight-kluster och placera den i mappen.

    1. Använd SCP för att kopiera filerna från den lokala datorn till den lokala lagringen av hdinsight-klusterhuvudnoden.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Använd kommandot ssh för att ansluta till [klustret.](./hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan `CLUSTERNAME` genom att ersätta med namnet på klustret och ange sedan kommandot:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. Från din ssh-session använder du KOMMANDOT HDFS för att kopiera filen från din huvudnodlokal lagring till Azure Storage.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Skapa tabeller

Exempeldata är nu tillgängliga. Pipelinen kräver dock två Hive-tabeller för bearbetning,`rawFlights`en för inkommande data`flights`( ) och en för summerade data ( ). Skapa dessa tabeller i Ambari enligt följande.

1. Logga in på Ambari `http://headnodehost:8080`genom att navigera till .

2. Välj **Hive**i listan över tjänster .

    ![Apache Ambari tjänster lista välja Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Välj **Gå till visa** bredvid etiketten Hive View 2.0.

    ![Sammanfattningslista för Ambari Apache Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. I frågetextområdet klistrar du in `rawFlights` följande satser för att skapa tabellen. Tabellen `rawFlights` innehåller ett schema-på-läsa för CSV-filer i `/example/data/flights` mappen i Azure Storage.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. Välj **Kör** för att skapa tabellen.

    ![hdi ambari tjänster hive fråga](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Om du `flights` vill skapa tabellen ersätter du texten i frågetextområdet med följande satser. Tabellen `flights` är en Hive-hanterad tabell som partitionerar data som läses in i den efter år, månad och dag i månaden. Den här tabellen innehåller alla historiska flygdata, med den lägsta granulariteten som finns i källdata för en rad per flygning.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Välj **Kör** för att skapa tabellen.

## <a name="create-the-oozie-workflow"></a>Skapa Oozie-arbetsflödet

Pipelines bearbetar vanligtvis data i batchar med ett givet tidsintervall. I det här fallet bearbetar pipelinen flygdata dagligen. Den här metoden gör det möjligt för indata CSV-filer att komma fram dagligen, veckovis, månadsvis eller årligen.

Exempelarbetsflödet bearbetar flygdata dag för dag, i tre huvudsteg:

1. Kör en Hive-fråga för att extrahera data för den dagens datumintervall `rawFlights` från käll-CSV-filen som representeras av tabellen och infoga data i `flights` tabellen.
2. Kör en Hive-fråga för att dynamiskt skapa en mellanlagringstabell i Hive för dagen, som innehåller en kopia av flygdata som summeras efter dag och transportföretag.
3. Använd Apache Sqoop för att kopiera alla data från den `dailyflights` dagliga mellanlagringstabellen i Hive till måltabellen i Azure SQL Database. Sqoop läser källraderna från data bakom Hive-tabellen som finns i Azure Storage och läser in dem i SQL Database med en JDBC-anslutning.

Dessa tre steg samordnas av ett Oozie-arbetsflöde.

1. Skapa en fil som heter `job.properties`. Använd texten nedan som startinnehåll för filen.
Uppdatera sedan värdena för din specifika miljö. Tabellen under texten sammanfattar var och en av egenskaperna och anger var du kan hitta värdena för din egen miljö.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Egenskap | Värdekälla |
    | --- | --- |
    | nameNode | Den fullständiga sökvägen till Azure Storage Container som är ansluten till ditt HDInsight-kluster. |
    | jobbSpårare | Det interna värdnamnet till det aktiva klustrets YARN-huvudnod. På Ambaris hemsida väljer du YARN i listan över tjänster och väljer sedan Active Resource Manager. Värdnamnet URI visas högst upp på sidan. Lägg till porten 8050. |
    | queueName (queueName) | Namnet på den YARN-kö som används vid schemaläggning av Hive-åtgärderna. Lämna som standard. |
    | oozie.use.system.libpath | Lämna som sant. |
    | appBase (appBase) | Sökvägen till undermappen i Azure Storage där du distribuerar Oozie-arbetsflödet och stödfilerna. |
    | oozie.wf.application.path | Platsen för Oozie-arbetsflödet `workflow.xml` som ska köras. |
    | hiveScriptLoadPartition | Sökvägen i Azure Storage till `hive-load-flights-partition.hql`Hive-frågefilen . |
    | hiveScriptCreateDailyTable | Sökvägen i Azure Storage till `hive-create-daily-summary-table.hql`Hive-frågefilen . |
    | hiveDailyStanamn | Det dynamiskt genererade namnet som ska användas för mellanlagringstabellen. |
    | hiveDataFolder | Sökvägen i Azure Storage till de data som finns i mellanlagringstabellen. |
    | sqlDatabaseConnectionString | JDBC-syntaxanslutningssträngen till din Azure SQL-databas. |
    | sqlDatabaseTableName | Namnet på tabellen i Azure SQL Database där sammanfattningsrader infogas. Lämna `dailyflights`som . |
    | år | Årsdelen av den dag för vilken flygsammanfattningar beräknas. Gå som det är. |
    | månad | Månadskomponenten för den dag för vilken flygsammanfattningar beräknas. Gå som det är. |
    | day | Den dag i månaden komponent av den dag för vilken flygsammanfattningar beräknas. Gå som det är. |

1. Skapa en fil som heter `hive-load-flights-partition.hql`. Använd koden nedan som innehållet i filen.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
        FL_DATE,
        CARRIER,
        FL_NUM,
        ORIGIN,
        DEST,
        DEP_DELAY,
        ARR_DELAY,
        ACTUAL_ELAPSED_TIME,
        DISTANCE,
        YEAR,
        MONTH,
        DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Oozie-variabler använder `${variableName}`syntaxen . Dessa variabler anges `job.properties` i filen. Oozie ersätter de faktiska värdena vid körning.

1. Skapa en fil som heter `hive-create-daily-summary-table.hql`. Använd koden nedan som innehållet i filen.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER STRING,
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Den här frågan skapar en mellanlagringstabell som bara lagrar de sammanfattade data för en dag, notera SELECT-satsen som beräknar den genomsnittliga fördröjningen och det totala avståndet som flygs av transportören per dag. De data som infogas i den här tabellen lagras på en känd plats (sökvägen som indikeras av variabeln hiveDataFolder) så att de kan användas som källa för Sqoop i nästa steg.

1. Skapa en fil som heter `workflow.xml`. Använd koden nedan som innehållet i filen. Dessa steg ovan uttrycks som separata åtgärder i Oozie arbetsflödesfil.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

De två Hive-frågorna nås av deras sökväg i Azure Storage och `job.properties` de återstående variabelvärdena tillhandahålls av filen. Den här filen konfigurerar arbetsflödet så att det körs för datumet 3 januari 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>Distribuera och köra Oozie-arbetsflödet

Använd SCP från din bash-session för`workflow.xml`att distribuera ditt Oozie-arbetsflöde ( ), Hive-frågorna (`hive-load-flights-partition.hql` och `hive-create-daily-summary-table.hql`), och jobbkonfigurationen (`job.properties`).  I Oozie kan `job.properties` bara filen finnas på den lokala lagringen av headnoden. Alla andra filer måste lagras i HDFS, i det här fallet Azure Storage. Vilken Sqoop-åtgärd som används av arbetsflödet beror på en JDBC-drivrutin för kommunikation med din SQL-databas, som måste kopieras från huvudnoden till HDFS.

1. Skapa `load_flights_by_day` undermappen under användarens sökväg i den lokala lagringen av huvudnoden. Kör följande kommando från den öppna ssh-sessionen:

    ```bash
    mkdir load_flights_by_day
    ```

1. Kopiera alla filer i den `workflow.xml` `job.properties` aktuella katalogen `load_flights_by_day` (och filerna) upp till undermappen. Kör följande kommando från den lokala arbetsstationen:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Kopiera arbetsflödesfiler till HDFS. Kör följande kommandon från den öppna ssh-sessionen:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Kopiera `mssql-jdbc-7.0.0.jre8.jar` från den lokala huvudnoden till arbetsflödesmappen i HDFS. Kommandot Revidera efter behov om klustret innehåller en annan jar-fil. Revidera `workflow.xml` efter behov för att återspegla en annan jar-fil. Kör följande kommando från den öppna ssh-sessionen:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Kör arbetsflödet. Kör följande kommando från den öppna ssh-sessionen:

    ```bash
    oozie job -config job.properties -run
    ```

1. Observera statusen med Oozie-webbkonsolen. Inifrån Ambari väljer du **Oozie**, **Snabblänkar**och sedan **Oozie Web Console**. Välj Alla jobb under fliken **Arbetsflödesjobb** . **All Jobs**

    ![hdi oozie webbkonsol arbetsflöden](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. När statusen är LYCKADES, fråga SQL Database tabellen för att visa de infogade raderna. Med Hjälp av Azure-portalen navigerar du till fönstret för SQL-databasen, väljer **Verktyg**och öppnar **Frågeredigeraren**.

        SELECT * FROM dailyflights

Nu när arbetsflödet körs för den enda testdagen kan du radbrytas i arbetsflödet med en koordinator som schemalägger arbetsflödet så att det körs dagligen.

## <a name="run-the-workflow-with-a-coordinator"></a>Kör arbetsflödet med en koordinator

Om du vill schemalägga arbetsflödet så att det körs dagligen (eller alla dagar i ett datumintervall) kan du använda en koordinator. En koordinator definieras av `coordinator.xml`en XML-fil, till exempel:

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Som du kan se skickar majoriteten av koordinatorn bara konfigurationsinformation till arbetsflödesinstansen. Det finns dock några viktiga punkter att ropa ut.

* Punkt 1: `start` `end` Själva elementets `coordinator-app` attribut och attribut styr det tidsintervall som koordinatorn kör över.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    En koordinator ansvarar `start` `end` för att schemalägga åtgärder inom och `frequency` datumintervallet, enligt det intervall som anges av attributet. Varje schemalagd åtgärd i sin tur kör arbetsflödet som konfigurerat. I koordinatorns definition ovan är samordnaren konfigurerad för att köra åtgärder från 1 januari 2017 till 5 januari 2017. Frekvensen är inställd på en dag av [Oozie Expression Language](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) frekvensuttryck `${coord:days(1)}`. Detta resulterar i att koordinatorn schemalägger en åtgärd (och därmed arbetsflödet) en gång per dag. För datumintervall som finns tidigare, som i det här exemplet, kommer åtgärden att schemaläggas att köras utan dröjsmål. Början av det datum från vilket en åtgärd är schemalagd att köras kallas den *nominella tiden*. Om du till exempel vill bearbeta data för 1 januari 2017 schemalägger samordnaren åtgärden med en nominell tid 2017-01-01T00:00:00 GMT.

* Punkt 2: Inom arbetsflödets datumintervall anger elementet `dataset` var du ska leta i HDFS efter data för ett visst datumintervall och konfigurerar hur Oozie avgör om data är tillgängliga ännu för bearbetning.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Sökvägen till data i HDFS är dynamiskt byggd `uri-template` enligt uttrycket i elementet. I denna samordnare används också en frekvens på en dag med datauppsättningen. Start- och slutdatum för koordinatorelementet styr när åtgärderna schemaläggs `frequency` (och definierar sina nominella tider), men datauppsättningen `uri-template` `initial-instance` styr beräkningen av det datum som används för att konstruera . I det här fallet anger du den första instansen till en dag före samordnarens början för att säkerställa att den hämtar den första dagens (1/1/2017) värde av data. Datauppsättningens datumberäkning rullar framåt från `initial-instance` värdet (2016-12-31) som avancerar i steg om datamängdsfrekvens (en dag) tills det senaste datumet som inte passerar den nominella tiden som fastställts av samordnaren (2017-01-01T00:00:00 GMT för den första åtgärden).

    Det `done-flag` tomma elementet anger att när Oozie kontrollerar förekomsten av indata vid den angivna tidpunkten, oozie avgör uppgifter om tillgängliga genom närvaro av en katalog eller fil. I det här fallet är det förekomsten av en CSV-fil. Om det finns en csv-fil antar Oozie att data är klara och startar en arbetsflödesinstans för att bearbeta filen. Om det inte finns någon csv-fil närvarande, antar Oozie data är ännu inte redo och att köra arbetsflödet går in i ett vänteläge.

* Punkt 3: `data-in` Elementet anger den tidsstämpel som ska användas som `uri-template` nominell tid när värdena ersätts för den associerade datauppsättningen.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    I det här fallet anger `${coord:current(0)}`du instansen till uttrycket , vilket kan översättas till att använda den nominella tiden för åtgärden som ursprungligen schemalagts av samordnaren. Med andra ord, när samordnaren schemalägger åtgärden för att köras med en nominell tid på 2017-01-01, är sedan 01/01/2017 vad som används för att ersätta variablerna ÅR (2017) och MÅNAD (01) i URI-mallen. När URI-mallen har beräknats för den här instansen kontrollerar Oozie om den förväntade katalogen eller filen är tillgänglig och schemalägger nästa körning av arbetsflödet därefter.

De tre föregående punkterna kombineras för att ge en situation där samordnaren schemalägger bearbetningen av källdata på ett dag för dag-sätt.

* Punkt 1: Samordnaren börjar med ett nominellt datum 2017-01-01.

* Punkt 2: Oozie söker `sourceDataFolder/2017-01-FlightData.csv`efter data som finns i .

* Punkt 3: När Oozie hittar filen schemaläggs en instans av arbetsflödet som bearbetar data för 2017-01-01. Oozie fortsätter sedan att bearbeta för 2017-01-02. Denna utvärdering upprepas fram till men inte inklusive 2017-01-05.

Precis som med arbetsflöden definieras konfigurationen `job.properties` av en koordinator i en fil, som har en superuppsättning av de inställningar som används av arbetsflödet.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

De enda nya egenskaperna `job.properties` som introduceras i den här filen är:

| Egenskap | Värdekälla |
| --- | --- |
| oozie.coord.application.path | Anger platsen för `coordinator.xml` filen som innehåller Oozie-koordinatorn att köra. |
| hiveDailyTableNamePrefix | Prefixet som används när tabellnamnet för mellanlagringstabellen skapas dynamiskt. |
| hiveDataFolderPrefix | Prefixet för sökvägen där alla mellanlagringstabeller ska lagras. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Distribuera och kör Oozie-koordinatorn

Om du vill köra pipelinen med en koordinator fortsätter du på ett liknande sätt som för arbetsflödet, förutom att du arbetar från en mapp en nivå ovanför mappen som innehåller arbetsflödet. Den här mappkonventionen skiljer koordinaterna från arbetsflödena på disken, så att du kan associera en koordinator med olika underordnade arbetsflöden.

1. Använd SCP från den lokala datorn för att kopiera koordinatorfilerna upp till den lokala lagringen av huvudnoden i klustret.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH i huvudet nod.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Kopiera koordinatornsfilerna till HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Kör koordinatorn.

    ```bash
    oozie job -config job.properties -run
    ```

5. Verifiera status med Oozie Web Console, den här gången välja fliken **Koordinatorjobb** och sedan **Alla jobb**.

    ![Oozie Web Console Samordnare Jobb](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Välj en koordinatorinstans om du vill visa listan över schemalagda åtgärder. I det här fallet bör du se fyra åtgärder med nominella tider i intervallet 1/1/2017 till 1/4/2017.

    ![Oozie Web Console Samordnare Jobb](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Varje åtgärd i den här listan motsvarar en instans av arbetsflödet som bearbetar en dags data, där början av den dagen anges av den nominella tiden.

## <a name="next-steps"></a>Nästa steg

[Apache Oozie Dokumentation](https://oozie.apache.org/docs/4.2.0/index.html)
