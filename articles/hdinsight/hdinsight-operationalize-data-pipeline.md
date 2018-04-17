---
title: Operationalisera en data analytics pipeline - Azure | Microsoft Docs
description: Ställ in och kör ett exempel data pipeline som utlöses av nya data och ger kortare resultat.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: 7ac1ed0db15d91ef8af009c879c3634148826286
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operationalisera en pipeline för dataanalys

*Data pipelines* underly många lösningar för dataanalys. Som namnet antyder en data-pipeline använder rådata, rensar omformas den efter behov och sedan genomför normalt beräkningar eller aggregeringar innan de lagras bearbetade data. Bearbetade data används av klienter, rapporter eller API: er. En data-pipeline måste ange repeterbara resultat om enligt ett schema eller när den utlöses av nya data.

Den här artikeln beskriver hur du operationalisera dina data rörledningar för repeterbarhet, med hjälp av Oozie som körs på HDInsight Hadoop-kluster. I exempelscenariot får du via en pipeline för data som förbereder och bearbetar flygbolag svarta time series-data.

I följande scenario är indata en flat-fil som innehåller en uppsättning data rör sig i en månad. Den här svarta data omfattar information som ursprung och destination flygplats, miles leds, utgångspunkten och ankomst gånger och så vidare. Målet med denna pipeline är att sammanfatta dagliga flygbolag prestanda, där varje flygbolag som innehåller en rad för varje dag med de genomsnittliga utgångspunkten och ankomst fördröjningar i minuter, och de totala miles leds den dagen.

| ÅR | MÅNAD | DAY_OF_MONTH | OPERATÖR |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | SOM | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

Exempel pipeline väntar tills en ny tidsperiod svarta data anländer och lagrar den detaljerade flyginformation i datalagret Hive för långsiktig analys. Pipelinen skapar även en mycket mindre dataset som sammanfattar bara de dagliga svarta data. Den här dagliga svarta sammanfattningsdata skickas till en SQL-databas och tillhandahåller rapporter, såsom för en webbplats.

Följande diagram illustrerar exempel pipeline.

![Svarta Data Pipeline](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="oozie-solution-overview"></a>Oozie lösning: översikt

Den här pipelinen använder Apache Oozie körs på ett HDInsight Hadoop-kluster.

Oozie beskriver dess pipelines i *åtgärder*, *arbetsflöden*, och *koordinatorer*. Åtgärder avgör det faktiska arbetet för att utföra, till exempel köra en Hive-fråga. Sekvens med åtgärder definierar arbetsflöden. Koordinatorer definiera ett schema för när arbetsflödet körs. Koordinatorer kan också vänta på tillgängligheten för nya data innan du startar en instans av arbetsflödet.

Följande diagram visar den övergripande designen för det här exemplet Oozie pipeline.

![Oozie svarta Data i Pipeline](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Etablera Azure-resurser

Den här pipelinen kräver en Azure SQL Database och ett HDInsight Hadoop-kluster på samma plats. Azure SQL-databasen lagrar både sammanfattningsdata produceras av pipeline och arkivet Oozie-metadata.

#### <a name="provision-azure-sql-database"></a>Etablera Azure SQL-databas

1. Med hjälp av Azure portal, skapa en ny resursgrupp med namnet `oozie` som innehåller alla resurser som används av det här exemplet.
2. I den `oozie` resursgrupp, etablera en Azure SQL Server och databas. Du behöver inte en databas större än S1 Standard prisnivån.
3. Med hjälp av Azure portal, gå till fönstret för nyligen distribuerade SQL-databasen och välj **verktyg**.

    ![Knappen Verktyg](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Välj **frågeredigeraren**.

    ![Query Editor-knappen](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. I den **frågeredigeraren** väljer **inloggning**.

    ![Inloggningsknappen](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. Ange dina autentiseringsuppgifter för SQL-databasen och välj **OK**.

   ![Inloggningsformulär](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. I textområdet frågeredigeraren, anger du följande SQL-instruktioner för att skapa den `dailyflights` tabellen som lagrar sammanfattade data från varje körning av pipeline.

    ```
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

8. Välj **kör** att köra SQL-instruktioner.

    ![Knappen Kör](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

Din Azure SQL-databas är nu klar.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Etablera ett HDInsight Hadoop-kluster

1. Välj i Azure-portalen **+ ny** och Sök efter HDInsight.
2. Välj **Skapa**.
3. Ange ett unikt namn för klustret i rutan grunderna och välj din Azure-prenumeration.

    ![HDInsight-klustrets namn och din prenumeration](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. I den **kluster typen** väljer den **Hadoop** kluster typ, **Linux** operativsystemet och den senaste versionen av HDInsight-klustret. Lämna den **klustret nivå** på **Standard**.

    ![Typ av HDInsight-kluster](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Välj **Välj** tillämpa dina val av klustret.
6. Slutför den **grunderna** fönstret genom att ange ett inloggningslösenord och välja din `oozie` resurs grupp i listan och sedan markera **nästa**.

    ![Grunderna i HDInsight-fönstret](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. I den **lagring** rutan, lämna primära lagring skriver inställd på **Azure Storage**väljer **Skapa nytt**, och ange ett namn för det nya kontot.

    ![Inställningarna för HDInsight-Storage-konto](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. För den **Metastore inställningar**under **Välj en SQL-databas för Hive**, väljer du den databas du skapade tidigare.

    ![HDInsight Hive Metastore inställningar](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Välj **autentisera SQL-databas**.

    ![Autentisera för HDInsight Hive Metastore](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Ange ditt användarnamn för SQL-databasen och lösenord och välj **Välj**. 

       ![HDInsight Hive Metastore autentisera inloggningen](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Tillbaka på den **Metastore inställningar** fönstret, Välj din databas för Oozie-metadata lagra och autentisera som du gjorde tidigare. 

       ![HDInsight Metastore inställningar](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Välj **Nästa**.
13. På den **sammanfattning** väljer **skapa** att distribuera klustret.

### <a name="verify-ssh-tunneling-setup"></a>Kontrollera SSH tunnlar installationen

Om du vill använda webbkonsolen Oozie för att visa status för din coordinator och arbetsflödesinstanser, ange en SSH-tunnel till ditt HDInsight-kluster. Mer information finns i [SSH-Tunnel](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]
> Du kan också använda Chrome med den [Foxy Proxy](https://getfoxyproxy.org/) tillägg till Bläddra web klusterresurser över SSH-tunnel. Konfigurera till proxy alla begäranden via värden `localhost` på den tunneln port 9876. Den här metoden är kompatibel med Windows-undersystem för Linux, även kallat Bash på Windows 10.

1. Kör följande kommando för att öppna en SSH-tunnel i klustret:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Kontrollera tunneln fungerar genom att gå till Ambari i din huvudnod genom att bläddra till:

    http://headnodehost:8080

3. Att få åtkomst till den **Oozie webbkonsolen** inom Ambari, Välj **Oozie**, **snabblänkar**, och välj sedan **Oozie webbkonsolen**.

### <a name="configure-hive"></a>Konfigurera Hive

1. Hämta en exempel-CSV-fil som innehåller data som rör sig i en månad. Hämta dess ZIP-filen `2017-01-FlightData.zip` från den [HDInsight Github-lagringsplatsen](https://github.com/hdinsight/hdinsight-dev-guide) och packa upp den CSV-filen `2017-01-FlightData.csv`. 

2. Kopiera CSV-filen upp till Azure Storage-konto kopplat till ditt HDInsight-kluster och placera den i den `/example/data/flights` mapp.

Du kan kopiera filen med SCP i din `bash` shell session.

1. Använd SCP för att kopiera filerna från den lokala datorn till den lokala lagringen på ditt HDInsight-klustrets huvudnod.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Använd HDFS-kommando för att kopiera filen från din lokala huvudnod lagring till Azure Storage.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Exempeldata är nu tillgänglig. Pipelinen kräver dock två Hive-tabeller för bearbetning, en för inkommande data (`rawFlights`) och en för sammanfattningsdata (`flights`). Skapa dessa tabeller i Ambari på följande sätt.

1. Logga in på Ambari genom att gå till [ http://headnodehost:8080 ](http://headnodehost:8080).
2. Välj i listan över tjänster **Hive**.

    ![Att välja Hive i Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Välj **går du till vyn** bredvid Hive visa 2.0 etiketten.

    ![Att välja Hive-vyn i Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Klistra in följande instruktioner för att skapa i textområde frågan i `rawFlights` tabell. Den `rawFlights` tabell innehåller en schema-på-läsning för CSV-filer i den `/example/data/flights` mappen i Azure Storage. 

    ```
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

5. Välj **Execute** att skapa tabellen.

    ![Hive-fråga i Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Att skapa den `flights` tabell, ersätter text i frågeområdet text med följande instruktioner. Den `flights` tabellen är en hanterad Hive-tabell som partitionerar data läses in i den av året, månaden och dagen i månaden. Den här tabellen innehåller alla historiska svarta data med den lägsta Granulariteten finns i källdata till en rad per svarta.

    ```
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

7. Välj **Execute** att skapa tabellen.

### <a name="create-the-oozie-workflow"></a>Skapa Oozie-arbetsflöde

Pipelines bearbeta data i batchar vanligtvis av ett visst tidsintervall. I det här fallet bearbetar pipeline svarta data varje dag. Den här metoden möjliggör för inkommande CSV-filerna till varje dag, varje vecka, månad eller per år.

Exempel arbetsflödet bearbetar den svarta data dag för dag, i tre steg:

1. Köra en Hive-fråga om du vill extrahera data för den dagen datumintervall från käll-CSV-fil som representeras av den `rawFlights` tabell och infoga data i den `flights` tabellen.
2. Köra en Hive-fråga för att dynamiskt skapa en mellanlagringstabellen i Hive för dagen, som innehåller en kopia av den svarta data Sammanfattad per dag och Operatörsnamn.
3. Använd Apache Sqoop för att kopiera alla data från den dagliga mellanlagringstabellen i Hive till målet `dailyflights` tabell i Azure SQL Database. Sqoop läser källraderna från bakomliggande Hive-tabell som finns i Azure Storage och läser in dem i SQL-databasen med en JDBC-anslutning.

De här tre stegen koordineras av ett arbetsflöde för Oozie. 

1. Skapa en fråga i filen `hive-load-flights-partition.hql`.

    ```
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

    Oozie variabler syntaxen `${variableName}`. Dessa variabler har angetts i den `job.properties` filen enligt beskrivningen i ett senare skede. Oozie ersätter de faktiska värdena vid körning.

2. Skapa en fråga i filen `hive-create-daily-summary-table.hql`.

    ```
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

    Den här frågan skapar en mellanlagringstabellen som ska lagra sammanfattade data under en dag, anteckna SELECT-instruktion som beräknar genomsnittligt fördröjningar och totalt avståndet leds av operatör per dag. De data som infogas i den här tabellen lagras på en känd plats (sökväg som anges av variabeln hiveDataFolder) så att den kan användas som källa för Sqoop i nästa steg.

3. Kör följande kommando för Sqoop.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

De här tre stegen uttrycks i tre olika åtgärder i följande Oozie arbetsflödesfil med namnet `workflow.xml`.

```
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
            <archive>sqljdbc41.jar</archive>
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

Två Hive-frågor via deras sökväg i Azure Storage och de återstående variabelvärdena tillhandahålls av följande `job.properties` fil. Den här filen konfigurerar arbetsflödet ska köras för dag 3 januari 2017.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

I följande tabell sammanfattas varje egenskap och anger var du hittar värdena för din egen miljö.

| Egenskap | Värdekälla |
| --- | --- |
| NameNode | Den fullständiga sökvägen till Azure Storage-behållare som är kopplad till ditt HDInsight-kluster. |
| jobTracker | Internt värdnamnet för ditt active kluster YARN head nod. På startsidan Ambari Välj YARN från listan över tjänster och sedan Active Resource Manager. Värdnamnet URI visas överst på sidan. Lägg till port 8050. |
| Könamn | Namnet på den YARN-kö som används vid schemaläggning av Hive-åtgärder. Lämna som standard. |
| oozie.Use.system.libpath | Lämna som SANT. |
| programbasen | Sökvägen till undermappen i Azure Storage där du distribuerar Oozie arbetsflödet och stödfiler. |
| oozie.WF.Application.PATH | Platsen för Oozie-arbetsflödet `workflow.xml` ska köras. |
| hiveScriptLoadPartition | I Azure Storage sökvägen till filen Hive `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | I Azure Storage sökvägen till filen Hive `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | Det dynamiskt genererade namnet för mellanlagringstabellen. |
| hiveDataFolder | Sökvägen i Azure Storage till de data som finns i mellanlagringstabellen. |
| sqlDatabaseConnectionString | JDBC syntax anslutningssträngen till din Azure SQL-databas. |
| sqlDatabaseTableName | Namnet på tabellen i Azure SQL Database som sammanfattning rader infogas. Lämna som `dailyflights`. |
| år | Årskomponenten på dagen för vilka svarta sammanfattningar beräknas. Låt stå. |
| månad | Månadskomponenten på dagen för vilka svarta sammanfattningar beräknas. Låt stå. |
| dag | Dagen i månaden komponent på dagen för vilka svarta sammanfattningar beräknas. Låt stå. |

> [!NOTE]
> Se till att uppdatera din kopia av den `job.properties` fil med värdena som är specifika för din miljö innan du kan distribuera och köra Oozie arbetsflödet.

### <a name="deploy-and-run-the-oozie-workflow"></a>Distribuera och köra Oozie-arbetsflöde

Använda SCP från bash-session för att distribuera Oozie arbetsflödet (`workflow.xml`), Hive-frågor (`hive-load-flights-partition.hql` och `hive-create-daily-summary-table.hql`) och jobbkonfigurationen som (`job.properties`).  I Oozie endast den `job.properties` filen kan finnas på den lokala lagringen på headnode. Alla filer måste vara lagrad i HDFS i det här fallet Azure Storage. Åtgärden Sqoop används av arbetsflödet är beroende av en JDBC-drivrutin för att kommunicera med din SQL-databas måste kopieras från huvudnod till HDFS.

1. Skapa den `load_flights_by_day` undermapp under användarens sökväg i den lokala lagringen på huvudnoden.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Kopiera alla filer i den aktuella katalogen (den `workflow.xml` och `job.properties` filer) upp till den `load_flights_by_day` undermappen.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. SSH i din huvudnod och navigera till den `load_flights_by_day` mapp.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Kopiera Arbetsflödesfilerna till HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Kopiera `sqljdbc41.jar` från lokala huvudnod till mappen arbetsflödet i HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Köra arbetsflödet.

        oozie job -config job.properties -run

7. Se status i webbkonsolen Oozie. Inom Ambari, Välj **Oozie**, **snabblänkar**, och sedan **Oozie webbkonsolen**. Under den **arbetsflödesjobb** väljer **alla jobb**.

    ![Oozie Web Console-arbetsflöden](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. När statusen lyckades, fråga om du vill visa de infogade raderna SQL-databastabell. Med hjälp av Azure portal, gå till fönstret för SQL-databasen, väljer **verktyg**, och öppna den **frågeredigeraren**.

        SELECT * FROM dailyflights

Nu när arbetsflödet körs för den enda test dagen omsluter du det här arbetsflödet med koordinator som schemalägger arbetsflödet så att den körs varje dag.

### <a name="run-the-workflow-with-a-coordinator"></a>Kör arbetsflödet med koordinator

Om du vill schemalägga arbetsflödet så att det körs varje dag (eller alla dagar i ett datumintervall), kan du använda en koordinator. Koordinator definieras av en XML-fil, till exempel `coordinator.xml`:

```
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

Som du kan se är flesta koordinatorn bara överföring av konfigurationsinformation till arbetsflödesinstansen. Det finns dock några viktiga element att anropa.

* Punkt 1: Den `start` och `end` attribut på den `coordinator-app` elementet i sig själv styra den tidsperioden under vilken koordinatorn körs.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Koordinator ansvarar för schemaläggning av åtgärder i den `start` och `end` intervallet enligt det intervall som anges av den `frequency` attribut. Alla schemalagda åtgärder körs i sin tur arbetsflödet som konfigurerats. I coordinator definitionen ovan konfigureras koordinatorn för att köra åtgärder från den 1 januari 2017 till 5 januari 2017. Frekvensen är inställd på 1 dag efter den [Oozie Expression Language](http://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) frekvens uttrycket `${coord:days(1)}`. Detta resulterar i koordinatorn schemaläggning av en åtgärd (och därför arbetsflödet) en gång per dag. Åtgärden kommer att schemaläggas att köras utan fördröjning för datumintervall som finns i tidigare i det här exemplet. Början av det datum som en åtgärd har schemalagts att köras kallas den *nominell tid*. Till exempel för att bearbeta data för den 1 januari 2017 koordinatorn schemalägger åtgärden med en nominell 2017-01-01T00:00:00 GMT.

* Punkt 2: inom det angivna datumintervallet för arbetsflödet, den `dataset` elementet anger var i HDFS data för ett visst datumintervall och konfigurerar hur Oozie avgör om data är tillgängliga för bearbetning.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Sökvägen till data i HDFS skapas dynamiskt enligt uttryck som anges i den `uri-template` element. I den här coordinator används även en frekvens som en dag med datauppsättningen. När start- och slutdatum på coordinator elementet kontrollen när åtgärderna som är schemalagda (och definierar deras nominell tid), den `initial-instance` och `frequency` för datamängden styra beräkning av det datum som används i hur du skapar `uri-template`. I så fall, ange den första instansen till en dag före coordinator så att den hämtar den första dagen är (1/1/2017) kan du se. Beräkning av dataset rullar framåt från värdet för `initial-instance` (2016/12/31) Avancera i steg om datauppsättningsfrekvensen (1 dag) tills den hittar det senaste datumet som inte klarar nominell tid anges av koordinatorn (2017-01-01T00:00:00 GMT för den första åtgärden).

    Tomt `done-flag` elementet anger att när Oozie söker efter förekomsten av indata vid tidpunkt, Oozie avgör data om tillgängliga av förekomsten av en katalog eller fil. I det här fallet är det förekomsten av en csv-fil. Om det finns en csv-fil, förutsätter Oozie att data är klar och startar en arbetsflödesinstans för att bearbeta filen. Om det finns ingen csv-fil som finns, förutsätter Oozie att data är ännu inte klar och den körningen av arbetsflödet försätts i väntande läge.

* Punkt 3: Den `data-in` element anger viss tidsstämpeln ska användas som nominellt tid när du ersätter värdena i `uri-template` för associerad dataset.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    I det här fallet anger instansen till uttryck `${coord:current(0)}`, vilket innebär att använder nominell tiden på den åtgärd som schemalagt av koordinatorn. Med andra ord, när koordinatorn schemalägger åtgärden som ska köras med en nominell 2017-01/01, är 01/01/2017 som används för att ersätta år (2017) och månad (01) variabler i URI-mall. När URI-mall beräknas för den här instansen, kontrollerar Oozie om förväntade katalogen eller filen finns och därför schemalägger nästa körning av arbetsflödet.

De tre föregående punkterna tillsammans ger en situation där koordinatorn schemalägger bearbetning av data på ett sätt för dag för dag. 

* Punkt 1: Koordinatorn börjar med en nominell datum för 2017-01-01.

* Punkt 2: Oozie söker efter data som är tillgängliga i `sourceDataFolder/2017-01-FlightData.csv`.

* Punkt 3: När Oozie hittar filen, den schemaläggs en instans av arbetsflödet som bearbetar data för 2017-01-01. Oozie fortsätter sedan bearbetningen för 2017-01-02. Utvärderingen upprepas upp till men inte 2017-01-05.

Som med arbetsflöden, konfigurationen av en koordinator har definierats i en `job.properties` fil som har en supermängd av inställningarna som används av arbetsflödet.

```
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=hn0-[CLUSTERNAME].[UNIQUESTRING].dx.internal.cloudapp.net:8050
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

Endast nya egenskaper som introducerades i detta `job.properties` filen är:

| Egenskap | Värdekälla |
| --- | --- |
| oozie.coord.Application.PATH | Anger platsen för den `coordinator.xml` -fil som innehåller Oozie-koordinator för att köra. |
| hiveDailyTableNamePrefix | Ett prefix för att dynamiskt skapa tabellnamnet för mellanlagringstabellen. |
| hiveDataFolderPrefix | Prefix för sökvägen där alla mellanlagringstabellerna ska lagras. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Distribuera och köra Oozie-koordinator

Om du vill köra pipelinen med koordinator fortsätta på ett liknande sätt som för arbetsflödet, förutom en nivå ovanför den mapp som innehåller ditt arbetsflöde arbeta från en mapp. Den här mappen konventionen skiljer koordinatörerna från arbetsflöden på disk, så att du kan associera en koordinator med olika underordnade arbetsflöden.

1. Använd SCP från din lokala dator för att kopiera koordinator filer till lokal lagring av huvudnod i klustret.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. SSH i din huvudnod.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Kopiera koordinator filer till HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Kör koordinatorn.

    ```bash
    oozie job -config job.properties -run
    ```

5. Kontrollera status i webbkonsolen Oozie denna tid att välja den **Coordinator jobb** fliken och sedan **alla jobb**.

    ![Oozie Web Console Coordinator jobb](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Välj en koordinator-instans för att visa listan över schemalagda åtgärder. I det här fallet bör du se fyra åtgärder med nominell gånger i intervallet från 1/1/2017 till 1/4/2017.

    ![Oozie Web Console Coordinator jobb](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Varje åtgärd i den här listan motsvarar en instans av arbetsflödet som bearbetar en dagars data, om början på dagen anges av den nominella tid.

## <a name="next-steps"></a>Nästa steg

* [Apache Oozie-dokumentationen](http://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
