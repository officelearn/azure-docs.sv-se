---
title: Operationalisera en pipeline för dataanalys – Azure
description: Konfigurera och kör en exempel-datapipeline som utlöses av nya data och ger kortfattade resultat.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 175fdcc1bf8d28c0eeb6eeccaa54c996c837ef81
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744453"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operationalisera en pipeline för dataanalys

*Datapipelines* underly många lösningar för dataanalys. Som namnet antyder en datapipeline tar in rådata, rensar omformas den efter behov och sedan vanligtvis utför beräkningar eller aggregeringar innan du lagrar bearbetade data. Bearbetade data används av klienter, rapporter eller API: er. En datapipeline måste ange repeterbara resultat om enligt ett schema eller när det aktiveras av nya data.

Den här artikeln beskriver hur du operationaliserar dina datapipelines för repeterbarhet, med hjälp av Oozie som körs på HDInsight Hadoop-kluster. I exempelscenariot får du via en datapipeline som förbereder och bearbeta flygbolag flygning time series-data.

I följande scenario är indata en platt fil som innehåller en batch med flygning data i en månad. Den här flygning data omfattar information som flygplatsen original och beskrivning, miles leds, avgång och ankomst gånger och så vidare. Målet med denna pipeline är att sammanfatta dagliga flygbolag prestanda, där varje flygbolag har en rad för varje dag med de genomsnittliga avgång och ankomst fördröjningar i minuter och totala miles leds den dagen.

| YEAR | MONTH | DAY_OF_MONTH | OPERATÖR |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | AA | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | DL | 6.935409 | -2.1893024 | 1909696 |

Exempel-pipeline väntar tills en ny tidsperiod flygning data tas emot och lagrar den detaljerade flyginformation i Apache Hive data warehouse för långsiktig analyser. Pipelinen skapar även en mycket mindre datauppsättning som sammanfattar bara den dagliga datavolymen flygning. Den här dagliga flygning sammanfattningsdata skickas till en SQL-databas och tillhandahåller rapporter, till exempel för en webbplats.

Följande diagram illustrerar arbetsflödet i exemplet.

![Flight Datapipeline](./media/hdinsight-operationalize-data-pipeline/pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Översikt över Apache Oozie-lösning

Denna pipeline använder Apache Oozie som körs på ett HDInsight Hadoop-kluster.

Oozie beskriver dess pipelines i *åtgärder*, *arbetsflöden*, och *koordinatorer*. Åtgärder avgör det faktiska arbetet att utföra, till exempel köra en Hive-fråga. Sekvens med åtgärder definierar arbetsflöden. Koordinatorer definiera schemat för när arbetsflödet körs. Koordinatorer kan också vänta på tillgängligheten för nya data innan du startar en instans av arbetsflödet.

Följande diagram visar den övergripande designen av det här exemplet Oozie-pipeline.

![Oozie flygning Datapipeline](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

### <a name="provision-azure-resources"></a>Etablera Azure-resurser

Den här pipelinen kräver en Azure SQL-databas och ett HDInsight Hadoop-kluster på samma plats. Azure SQL Database lagrar både de sammanfattning av data som produceras av pipelinen och arkivet Oozie-metadata.

#### <a name="provision-azure-sql-database"></a>Etablera Azure SQL-databas

1. Med Azure portal, skapa en ny resursgrupp med namnet `oozie` som innehåller alla resurser som används av det här exemplet.
2. I den `oozie` resursgrupp, etablera en Azure SQL Server och databas. Du behöver inte en databas större än prisnivån S1 Standard.
3. Med Azure-portalen, gå till fönstret för nyligen distribuerade SQL-databasen och välj **verktyg**.

    ![Verktyg](./media/hdinsight-operationalize-data-pipeline/sql-db-tools.png)

4. Välj **frågeredigeraren**.

    ![Query Editor-knappen](./media/hdinsight-operationalize-data-pipeline/sql-db-query-editor.png)

5. I den **frågeredigeraren** väljer **inloggning**.

    ![Inloggningsknappen](./media/hdinsight-operationalize-data-pipeline/sql-db-login1.png)

6. Ange dina autentiseringsuppgifter för SQL-databas och välj **OK**.

   ![Inloggningsformulär](./media/hdinsight-operationalize-data-pipeline/sql-db-login2.png)

7. I textområdet frågeredigeraren, anger du följande SQL-uttryck för att skapa den `dailyflights` tabellen som lagrar sammanfattade data från varje körning av pipelinen.

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

8. Välj **kör** att köra SQL-uttryck.

    ![Köra knappen](./media/hdinsight-operationalize-data-pipeline/sql-db-run.png)

Din Azure SQL Database är nu klar.

#### <a name="provision-an-hdinsight-hadoop-cluster"></a>Etablera ett HDInsight Hadoop-kluster

1. I Azure-portalen väljer du **+ ny** och Sök efter HDInsight.
2. Välj **Skapa**.
3. Ange ett unikt namn för klustret och välj din Azure-prenumeration i fönstret grunder.

    ![HDInsight-klusternamnet och prenumeration](./media/hdinsight-operationalize-data-pipeline/hdi-name-sub.png)

4. I den **Klustertyp** väljer den **Hadoop** klustertyp, **Linux** operativsystemet och den senaste versionen av HDInsight-klustret. Lämna den **kluster nivån** på **Standard**.

    ![Typ av HDInsight-kluster](./media/hdinsight-operationalize-data-pipeline/hdi-cluster-type.png)

5. Välj **Välj** att tillämpa dina val av klustret.
6. Slutför den **grunderna** genom att ange ett inloggningslösenord och välja din `oozie` resource gruppen i listan och välj sedan **nästa**.

    ![Grunderna i HDInsight-fönstret](./media/hdinsight-operationalize-data-pipeline/hdi-basics.png)

7. I den **Storage** fönstret kan du låta den primära lagringen skriver inställt **Azure Storage**väljer **Skapa nytt**, och ange ett namn för det nya kontot.

    ![Inställningar för Lagringskonto för HDInsight](./media/hdinsight-operationalize-data-pipeline/hdi-storage.png)

8. För den **Metaarkivsinställningar**under **Välj en SQL-databas för Hive**, väljer du den databas som du skapade tidigare.

    ![Inställningar för HDInsight Hive-Metaarkiv](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-hive.png)

9. Välj **autentisera SQL Database**.

    ![Autentisera för HDInsight Hive-Metaarkiv](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql.png)

10. Ange ditt SQL database-användarnamn och lösenord och välj **Välj**. 

       ![HDInsight Hive-Metaarkiv autentisera inloggningen](./media/hdinsight-operationalize-data-pipeline/hdi-authenticate-sql-login.png)

11. Gå tillbaka till den **Metaarkivsinställningar** panelen, väljer din databas för Oozie-metadata lagra och autentisera som du gjorde tidigare. 

       ![HDInsight Metaarkivsinställningar](./media/hdinsight-operationalize-data-pipeline/hdi-metastore-settings.png)

12. Välj **Nästa**.
13. På den **sammanfattning** väljer **skapa** att distribuera klustret.

### <a name="verify-ssh-tunneling-setup"></a>Kontrollera SSH tunneling installationen

Om du vill använda Oozie Web Console för att visa status för dina coordinator och arbetsflödesinstanser, ställer du in en SSH-tunnel till ditt HDInsight-kluster. Mer information finns i [SSH-Tunnel](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Du kan också använda Chrome med den [Foxy Proxy](https://getfoxyproxy.org/) tillägget Bläddra web klusterresurser via SSH-tunnel. Konfigurera den att proxy alla begäranden via värden `localhost` på den tunnel-port 9876. Den här metoden är kompatibelt med Windows-undersystem för Linux, även känt som Bash i Windows 10.

1. Kör följande kommando för att öppna en SSH-tunnel till ditt kluster:

    ```
    ssh -C2qTnNf -D 9876 sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
    ```

2. Kontrollera tunneln fungerar genom att gå till Ambari på din huvudnoden genom att bläddra till:

    http://headnodehost:8080

3. Åtkomst till den **Oozie webbkonsolen** från Ambari, väljer **Oozie**, **snabblänkar**, och välj sedan **Oozie webbkonsolen**.

### <a name="configure-hive"></a>Konfigurera Hive

1. Hämta en CSV-exempelfil som innehåller data som rör sig i en månad. Ladda ned en ZIP-fil `2017-01-FlightData.zip` från den [HDInsight GitHub-lagringsplatsen](https://github.com/hdinsight/hdinsight-dev-guide) och packa upp den till CSV-filen `2017-01-FlightData.csv`. 

2. Kopiera den här CSV-filen upp till Azure Storage-konto som är kopplat till ditt HDInsight-kluster och placera den i den `/example/data/flights` mapp.

Du kan kopiera filen med SCP i din `bash` shell session.

1. Använd SCP för att kopiera filer från din lokala dator till den lokala lagringen av huvudnod ditt HDInsight-kluster.

    ```bash
    scp ./2017-01-FlightData.csv sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:2017-01-FlightData.csv
    ```

2. Använd HDFS-kommando för att kopiera filen från din lokala lagring huvudnoden till Azure Storage.

    ```bash
    hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
    ```

Exempeldata är nu tillgänglig. Pipelinen kräver dock två Hive-tabeller för bearbetning, en för inkommande (`rawFlights`) och en för sammanfattningsdata (`flights`). Skapa tabellerna i Ambari på följande sätt.

1. Logga in på Ambari genom att gå till [ http://headnodehost:8080 ](http://headnodehost:8080).
2. Välj i listan över tjänster **Hive**.

    ![Att välja Hive i Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Välj **går du till vyn** bredvid etiketten Hive visa 2.0.

    ![Att välja Hive-vyn i Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. I området fråga text, klistrar du in följande instruktioner att skapa den `rawFlights` tabell. Den `rawFlights` tabell innehåller ett schema vid läsning för CSV-filer i den `/example/data/flights` mapp i Azure Storage. 

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

5. Välj **kör** till att skapa tabellen.

    ![Hive-fråga i Ambari](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Att skapa den `flights` tabellen, Ersätt texten inom textområde frågan med följande satser. Den `flights` tabellen är en hanterad Hive-tabell som partitionerar data har lästs in av år, månad och dagen i månaden. Den här tabellen innehåller alla historiska flygning data med den lägsta Granulariteten finns i datakällan för en rad per flygning.

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

7. Välj **kör** till att skapa tabellen.

### <a name="create-the-oozie-workflow"></a>Skapa Oozie-arbetsflöde

Pipelines bearbeta normalt data i batchar med ett angivet tidsintervall. I det här fallet bearbetar pipelinen flygning data varje dag. Den här metoden kan CSV indatafilerna tas emot varje dag, varje vecka, varje månad eller år.

Exempelarbetsflöde bearbetar det rör sig data dag för dag, i tre steg:

1. Kör en Hive-fråga för att extrahera data för den dagen datumintervall från käll-CSV-filen som representeras av den `rawFlights` tabellen och infoga data i den `flights` tabell.
2. Kör en Hive-fråga för att dynamiskt skapa en mellanlagringstabellen i Hive för den dagen, som innehåller en kopia av det rör sig data sammanställt efter dag och operatör.
3. Använd Apache Sqoop för att kopiera alla data från den dagliga mellanlagringstabellen i Hive till målet `dailyflights` tabell i Azure SQL Database. Sqoop läser källraderna från data bakom Hive-tabell som finns i Azure Storage och läser in dem i SQL-databas med en JDBC-anslutning.

De här tre stegen koordineras av ett Oozie-arbetsflöde. 

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

    Använd syntax för Oozie variabler `${variableName}`. De här variablerna anges i den `job.properties` filen enligt beskrivningen i ett senare skede. Oozie ersätter de faktiska värdena vid körning.

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

    Den här frågan skapar en mellanlagringstabellen som ska lagra endast sammanfattade data under en dag, anteckna SELECT-instruktion som beräknar genomsnittliga fördröjningar och totalt avståndet som leds av operatör per dag. De data som infogats i den här tabellen som lagras på en känd plats (sökväg som anges med variabeln hiveDataFolder) så att den kan användas som källa för Sqoop i nästa steg.

3. Kör följande kommando för Sqoop.

    ```
    sqoop export --connect ${sqlDatabaseConnectionString} --table ${sqlDatabaseTableName} --export-dir ${hiveDataFolder} -m 1 --input-fields-terminated-by "\t"
    ```

De här tre stegen uttrycks i tre separata åtgärder i följande Oozie-arbetsflödesfil med namnet `workflow.xml`.

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

Två Hive-frågor som används av deras sökvägen i Azure Storage och de återstående variabelvärdena tillhandahålls av följande `job.properties` fil. Den här filen konfigurerar arbetsflödet ska köras för datumet som den 3 januari 2017.

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

I följande tabell sammanfattas var och en av egenskaperna och anger var du hittar värden för din egen miljö.

| Egenskap  | Värdekälla |
| --- | --- |
| NameNode | Den fullständiga sökvägen till Azure Storage-behållare som är kopplad till ditt HDInsight-kluster. |
| jobTracker | För interna värdnamnet till din aktiva klustret YARN gå noden. På startsidan Ambari Välj YARN från listan över tjänster och välj sedan aktiv Resource Manager. Värdnamnet URI visas överst på sidan. Lägga till porten 8050. |
| Könamn | Namnet på den YARN-kö som används när du schemalägger Hive-åtgärder. Lämna som standard. |
| oozie.Use.system.libpath | Lämna som true. |
| programbasen | Sökvägen till undermappen i Azure Storage där du distribuerar Oozie-arbetsflöde och stödfiler. |
| oozie.WF.Application.PATH | Platsen för Oozie-arbetsflöde `workflow.xml` ska köras. |
| hiveScriptLoadPartition | Sökvägen i Azure Storage till Hive-fråga filen `hive-load-flights-partition.hql`. |
| hiveScriptCreateDailyTable | Sökvägen i Azure Storage till Hive-fråga filen `hive-create-daily-summary-table.hql`. |
| hiveDailyTableName | Det dynamiskt genererade namnet för mellanlagringstabellen. |
| hiveDataFolder | Sökvägen i Azure Storage till data i mellanlagringstabellen. |
| sqlDatabaseConnectionString | JDBC syntax anslutningssträngen till din Azure SQL Database. |
| sqlDatabaseTableName | Namnet på tabellen i Azure SQL Database där sammanfattning raderna infogas. Lämna som `dailyflights`. |
| år | Årskomponenten för den dagen för vilka flygning sammanfattningar beräknas. Lämna skick. |
| månad | Månadskomponenten för den dagen för vilka flygning sammanfattningar beräknas. Lämna skick. |
| dag | Dagen i månadskomponenten för den dagen för vilka flygning sammanfattningar beräknas. Lämna skick. |

> [!NOTE]  
> Se till att uppdatera din kopia av den `job.properties` filen med värdena som är specifika för din miljö innan du kan distribuera och kör din Oozie-arbetsflöde.

### <a name="deploy-and-run-the-oozie-workflow"></a>Distribuera och köra Oozie-arbetsflöde

Använd SCP från bash-session för att distribuera din Oozie-arbetsflöde (`workflow.xml`), Hive-frågor (`hive-load-flights-partition.hql` och `hive-create-daily-summary-table.hql`) och jobbkonfigurationen (`job.properties`).  I Oozie endast den `job.properties` filen kan finnas på den lokala lagringen för huvudnoden. Alla övriga filer måste lagras i HDFS, i det här fallet Azure Storage. Åtgärden Sqoop som används av arbetsflödet är beroende av en JDBC-drivrutin för att kommunicera med din SQL-databas, som kopieras från huvudnoden till HDFS.

1. Skapa den `load_flights_by_day` undermapp under användarens sökväg i den lokala lagringen för huvudnoden.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 'mkdir load_flights_by_day'

2. Kopiera alla filer i den aktuella katalogen (den `workflow.xml` och `job.properties` filer) upp till den `load_flights_by_day` undermappen.

        scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:load_flights_by_day

3. SSH till din huvudnod och navigera till den `load_flights_by_day` mapp.

        ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net
        cd load_flights_by_day

4. Kopiera Arbetsflödesfilerna till HDFS.

        hdfs dfs -put ./* /oozie/load_flights_by_day

5. Kopiera `sqljdbc41.jar` från den lokala huvudnoden till mappen arbetsflöde i HDFS:

        hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /oozie/load_flights_by_day

6. Köra arbetsflödet.

        oozie job -config job.properties -run

7. Notera att status med Oozie-webbkonsolen. Från Ambari, väljer **Oozie**, **snabblänkar**, och sedan **Oozie webbkonsolen**. Under den **arbetsflödesjobb** fliken **alla jobb**.

    ![Oozie Web Console arbetsflöden](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

8. När statusen är SLUTFÖRT, fråga SQL-databastabell om du vill visa de Infoga raderna. Med Azure-portalen, gå till fönstret för SQL-databasen, Välj **verktyg**, och öppna den **frågeredigeraren**.

        SELECT * FROM dailyflights

Nu när arbetsflödet körs för test av enkel-dag, kan du omsluta det här arbetsflödet med en koordinator som schemalägger arbetsflödet så att det körs varje dag.

### <a name="run-the-workflow-with-a-coordinator"></a>Kör arbetsflödet med en koordinator

Om du vill schemalägga det här arbetsflödet så att det körs varje dag (eller alla dagar i ett visst datumintervall), kan du använda en koordinator. En koordinator definieras av en XML-fil, till exempel `coordinator.xml`:

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

Som du ser är majoriteten av koordinatorn bara skicka konfigurationsinformation till arbetsflödesinstansen. Det finns dock några viktiga saker att lyfta fram.

* Punkt 1: Den `start` och `end` attribut på den `coordinator-app` elementet i sig själv styr det tidsintervall som koordinatorn körs.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    En koordinator ansvarar för schemaläggning av åtgärder inom den `start` och `end` datumintervall, enligt det intervall som anges av den `frequency` attribut. Varje schemalagd åtgärd körs i sin tur arbetsflödet som konfigurerats. I coordinator definitionen ovan konfigureras koordinatorn för att köra åtgärder från den 1 januari 2017 till den 5 januari 2017. Frekvensen är inställd på 1 dag av den [Oozie Uttrycksspråk](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) frekvens uttryck `${coord:days(1)}`. Detta resulterar i koordinatorn schemaläggning av en åtgärd (och därför arbetsflödet) en gång per dag. Åtgärden kommer att schemaläggas att köras utan fördröjning för datumintervall som är tidigare, som i följande exempel. Början av det datum som en åtgärd är schemalagd att köras kallas den *nominell tid*. Till exempel för att bearbeta data för den 1 januari 2017 koordinatorn schemalägger åtgärden med en nominell 2017-01-01T00:00:00 GMT.

* Punkt 2: Inom det angivna datumintervallet av arbetsflödet, den `dataset` elementet anger var att leta efter i HDFS data för ett visst datumintervall och konfigurerar hur Oozie anger om aktuella data är tillgängliga för bearbetning.

    ```
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Sökväg till data i HDFS skapas dynamiskt enligt de uttryck som anges i den `uri-template` element. I den här coordinator används också en frekvens på en dag med datauppsättningen. Medan start- och slutdatumen i coordinator elementet kontroll när åtgärderna som är schemalagda (och definierar sina nominell tid), den `initial-instance` och `frequency` på datauppsättningen styra beräkning av det datum som används i konstruera `uri-template`. I så fall, ange den första instansen till en dag före koordinatorn så att den hämtar den första dagen är (1/1/2017) tillhandahåller data. Datauppsättningens beräkning samlar framåt från värdet för `initial-instance` (2016/12/31) Avancera i steg om datauppsättningsfrekvensen (1 dag) tills den hittar det senaste datumet som inte klarar nominell tiden som koordinatorn (2017-01-01T00:00:00 GMT för den första åtgärden).

    Tomt `done-flag` elementet anger att när Oozie söker efter indata vid tidpunkt, Oozie avgör data om tillgängliga av förekomsten av en katalog eller fil. I det här fallet är det förekomsten av en csv-fil. Om en csv-fil finns förutsätter Oozie data är klar och startar en arbetsflödesinstans för att bearbeta filen. Om det finns ingen csv-fil som finns, förutsätter Oozie data är ännu inte är klara och som kör arbetsflödets hamnar i väntande läge.

* Punkt 3: Den `data-in` elementet anger viss tidsstämpeln ska användas som nominellt när du ersätter värdena i `uri-template` för den associerade datauppsättningen.

    ```
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    I det här fallet skalningsuppsättningsinstans uttryck `${coord:current(0)}`, vilket innebär att med hjälp av nominell tiden för åtgärden som det ursprungligen schemalagda med koordinatorn. Med andra ord, när koordinatorn schemalägger åtgärden som ska köras med en nominell 2017-01-01, är 01/01/2017 används för att ersätta år (2017) och månad (01) variabler i URI-mall. När mallen URI beräknas för den här instansen, kontrollerar Oozie om förväntade katalogen eller filen är tillgänglig och schemalägger nästa körning av arbetsflödet på lämpligt sätt.

De tre föregående punkterna kombineras för att ge en situation där koordinatorn schemalägger bearbetning av data på en dag för dag-sätt. 

* Punkt 1: Koordinatorn börjar med en nominell dagen för 2017-01-01.

* Punkt 2: Oozie söker efter data som är tillgängliga i `sourceDataFolder/2017-01-FlightData.csv`.

* Punkt 3: När Oozie hittar filen, schemalägger en instans av arbetsflödet som bearbetar data för 2017-01-01. Oozie sedan vidare bearbetning för 2017-01-02. Den här utvärderingsversionen upprepas fram till men inte inklusive 05-01-2017.

Som med arbetsflöden, konfigurationen av en koordinator har definierats i en `job.properties` -fil som har en överordnad uppsättning av inställningarna som används av arbetsflödet.

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

Endast nya egenskaper som presenteras i det här `job.properties` filen är:

| Egenskap  | Värdekälla |
| --- | --- |
| oozie.coord.Application.PATH | Platsen för den `coordinator.xml` -fil som innehåller Oozie-koordinator för att köra. |
| hiveDailyTableNamePrefix | Prefixet som används för att dynamiskt skapa tabellnamnet på mellanlagringstabellen. |
| hiveDataFolderPrefix | Prefix för sökvägen där alla mellanlagringstabeller kommer att lagras. |

### <a name="deploy-and-run-the-oozie-coordinator"></a>Distribuera och köra Oozie-koordinator

Om du vill köra en pipeline med en koordinator fortsätta på liknande sätt som för arbetsflödet, förutom att du arbetar från en mapp med en nivå ovanför den mapp som innehåller ditt arbetsflöde. Den här mappen konventionen skiljer koordinatorer från arbetsflöden på disk, så att du kan associera en koordinator med olika underordnade arbetsflöden.

1. Använd SCP från din lokala dator för att kopiera coordinator-filer till den lokala lagringen på huvudnoden för ditt kluster.

    ```bash
    scp ./* sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net:~
    ```

2. SSH till din huvudnoden.

    ```bash
    ssh sshuser@[CLUSTERNAME]-ssh.azurehdinsight.net 
    ```

3. Kopiera coordinator-filer till HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Kör koordinatorn.

    ```bash
    oozie job -config job.properties -run
    ```

5. Kontrollera statusen med webbkonsolen Oozie den här tiden att välja den **Coordinator jobb** fliken och sedan **alla jobb**.

    ![Oozie konsolen Coordinator Webbjobb](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Välj en koordinator instans att visa listan över schemalagda åtgärder. I det här fallet bör du se fyra åtgärder med nominell gånger mellan 2017-1-1 och 1/4/2017.

    ![Oozie konsolen Coordinator Webbjobb](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Varje åtgärd i den här listan motsvarar en instans av arbetsflödet som bearbetar en dags data, om den dagsstart anges med nominell tiden.

## <a name="next-steps"></a>Nästa steg

* [Apache Oozie-dokumentationen](https://oozie.apache.org/docs/4.2.0/index.html)

<!-- * Build the same pipeline [using Azure Data Factory](tbd.md).  -->
