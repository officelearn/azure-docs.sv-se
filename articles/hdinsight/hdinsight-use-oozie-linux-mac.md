---
title: Använda Hadoop Oozie arbetsflöden i Linux-baserade Azure HDInsight
description: Använda Oozie med Hadoop i Linux-baserade HDInsight. Lär dig hur du definierar ett Oozie-arbetsflöde och skicka en Oozie-jobb.
services: hdinsight
ms.service: hdinsight
ms.custom: hdinsightactive
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/26/2018
ms.openlocfilehash: 4ea8ded6abcdee397511272c539f9be6cdd12c0e
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685539"
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Använda Oozie med Hadoop för att definiera och köra ett arbetsflöde på Linux-baserade Azure HDInsight

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Lär dig använda Apache Oozie med Hadoop i Azure HDInsight. Oozie är ett arbetsflöde och koordination system som hanterar Hadoop-jobb. Oozie är integrerad med Hadoop-stacken och stöder följande jobb:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Du kan också använda Oozie för att schemalägga jobb som är specifika för ett system, t.ex. Java-program eller kommandoskript.

> [!NOTE]
> Ett annat alternativ att definiera arbetsflöden med HDInsight är att använda Azure Data Factory. Mer information om Data Factory finns [Använd Pig och Hive med Data Factory][azure-data-factory-pig-hive]. Använda Oozie i kluster med Enterprise Security Package finns [kör Apache Oozie i HDInsight Hadoop-kluster med Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).


## <a name="prerequisites"></a>Förutsättningar

* **En vanlig HDInsight-kluster**: se [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md)

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystem som används på HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="example-workflow"></a>Exempel på ett arbetsflöde

Arbetsflödet som används i det här dokumentet innehåller två åtgärder. Åtgärder är definitioner för aktiviteter, till exempel kör Hive, Sqoop, MapReduce eller andra processer:

![Arbetsflödesdiagram][img-workflow-diagram]

1. En Hive-åtgärd körs en HiveQL-skript för att extrahera poster från den **hivesampletable** som ingår i HDInsight. Varje datarad beskriver ett besök av specifika mobila enheter. Postformatet visas som följande text:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Hive-skriptet i det här dokumentet räknar Totalt antal besök för varje plattform, till exempel Android eller iPhone, och sparas antalet i ett nytt Hive-tabell.

    Mer information om Hive finns i [Använda Hive med HDInsight][hdinsight-use-hive].

2. En åtgärd som Sqoop exporterar innehållet i den nya Hive-tabellen till en tabell som skapats i Azure SQL Database. Läs mer om Sqoop [Använd Hadoop Sqoop med HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Versioner som stöds Oozie på HDInsight-kluster, se [Nyheter i Hadoop-klusterversionerna från HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Skapa arbetskatalogen

Oozie förväntar sig att du kan lagra alla resurser som krävs för ett jobb i samma katalog. Det här exemplet används **wasb: / / / självstudier/useoozie**. Utför följande steg för att skapa den här katalogen:

1. Anslut till HDInsight-klustret med hjälp av SSH:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Ersätt `sshuser` med SSH-användarnamnet för klustret. Ersätt `clustername` med namnet på klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. För att skapa katalogen, använder du följande kommando:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > Den `-p` parametern orsakar skapande av alla kataloger i sökvägen. Den **data** directory används för att rymma de data som används av den **useooziewf.hql** skript.

3. För att säkerställa att Oozie kan personifiera ditt konto, använder du följande kommando:

    ```bash
    sudo adduser username users
    ```

    Ersätt `username` med ditt SSH-användarnamn.

    > [!NOTE]
    > Du kan ignorera fel som indikerar användaren är redan medlem i den `users` grupp.

## <a name="add-a-database-driver"></a>Lägga till en databasdrivrutin

Eftersom det här arbetsflödet använder Sqoop för att exportera data till SQL-databasen, måste du ange en kopia av JDBC-drivrutinen används för att interagera med SQL-databasen. Om du vill kopiera JDBC-drivrutinen till arbetskatalogen, använder du följande kommando från SSH-sessionen:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

> [!NOTE]
> Du får ett meddelande om att filen redan finns.

Om arbetsflödet använder andra resurser, till exempel en JAR-filen som innehåller ett MapReduce-program, som du behöver lägga till dessa resurser också.

## <a name="define-the-hive-query"></a>Definiera Hive-fråga

Använd följande steg för att skapa en Hive-fråga (HiveQL) språkskript som definierar en fråga. Du använder frågan i ett Oozie-arbetsflöde senare i det här dokumentet.

1. Använd följande kommando från SSH-anslutning för att skapa en fil med namnet `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. När nanoredigeraren GNU öppnas, använder du följande fråga som innehållet i filen:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Det finns två variabler som används i skriptet:

    * `${hiveTableName}`: Innehåller namnet på tabellen som ska skapas.

    * `${hiveDataFolder}`: Innehåller platsen för att lagra datafiler för tabellen.

    Definitionsfilen för arbetsflödet, workflow.xml i den här självstudien skickar dessa värden till den här HiveQL-skript vid körning.

4. Välj Ctrl + X om du vill avsluta redigeraren. När du uppmanas, väljer `Y` för att spara filen, ange `useooziewf.hql` som filnamn och välj sedan **RETUR**.

5. Använd följande kommandon för att kopiera `useooziewf.hql` till `wasb:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Det här kommandot lagrar den `useooziewf.hql` filen i det HDFS-kompatibla lagringsutrymmet för klustret.

## <a name="define-the-workflow"></a>Definiera arbetsflödet

Oozie arbetsflödesdefinitioner skrivs i Hadoop processen Definition Language (hPDL), vilket är en definitionsspråk för XML-processen. Använd följande steg för att definiera arbetsflödet:

1. Använd följande instruktion för att skapa och redigera en ny fil:

    ```bash
    nano workflow.xml
    ```

2. När nanoredigeraren öppnas anger du följande XML som filinnehållet:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
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
            <arg>${hiveDataFolder}</arg>
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

    Det finns två åtgärder som definierats i arbetsflödet:

   * `RunHiveScript`: Den här åtgärden är startåtgärden och kör den `useooziewf.hql` Hive-skript.

   * `RunSqoopExport`: Den här åtgärden exporterar data som skapats från Hive-skriptet till en SQL-databas med hjälp av Sqoop. Den här åtgärden körs bara om den `RunHiveScript` åtgärden har genomförts.

     Arbetsflödet har flera poster, till exempel `${jobTracker}`. De här posterna ersätts med de värden som du använder i jobbdefinitionen. Du skapar jobbdefinitionen senare i det här dokumentet.

     Tänk också på den `<archive>sqljdbc4.jar</archive>` post i avsnittet Sqoop. Den här posten instruerar Oozie att tillgängliggöra det här arkivet för Sqoop när den här åtgärden körs.

3. Om du vill spara filen, Välj Ctrl + X, ange `Y`, och välj sedan **RETUR**. 

4. Använd följande kommando för att kopiera den `workflow.xml` filen till `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Skapa en databas

Om du vill skapa en SQL-databas, följer du stegen i den [skapa en SQL-databas](../sql-database/sql-database-get-started.md) dokumentet. När du skapar databasen använder `oozietest` som namnet på databasen. Även Anteckna namnet på databasservern.

### <a name="create-the-table"></a>Skapa tabellen

> [!NOTE]
> Det finns många sätt att ansluta till SQL-databas för att skapa en tabell. Följande steg använder [FreeTDS](http://www.freetds.org/) från HDInsight-klustret.


1. Använd följande kommando för att installera FreeTDS på HDInsight-kluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. När FreeTDS har installerats, använder du följande kommando för att ansluta till SQL database-server som du skapade tidigare:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Du får utdata som liknar följande text:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Vid uppmaningen `1>` anger du följande rader:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    När instruktionen `GO` har angivits värderas de föregående instruktionerna. De här uttrycken som skapar en tabell med namnet **mobiledata**, som används av arbetsflödet.

    Kontrollera att tabellen har skapats genom att använda följande kommandon:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Du ser utdata som liknar följande text:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Om du vill avsluta tsql-verktyget, ange `exit` på den `1>` prompten.

## <a name="create-the-job-definition"></a>Skapa jobbdefinitionen

Jobbdefinitionen beskrivs var du hittar workflow.xml. Här beskrivs också var du hittar andra filer som används av arbetsflödet, till exempel `useooziewf.hql`. Den definierar också värden för egenskaper som används i arbetsflödet och de associerade filerna.

1. Använd följande kommando för att hämta den fullständiga adressen för standardlagring. Den här adressen används i konfigurationsfilen som du skapar i nästa steg.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Det här kommandot returnerar information, till exempel följande XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Om HDInsight-klustret använder Azure Storage som standardlagring, den `<value>` elementet innehållet börjar med `wasb://`. Om Azure Data Lake Store används i stället, den börjar med `adl://`.

    Spara innehållet i den `<value>` elementet, eftersom den används i nästa steg.

2. Om du vill skapa definitionen för Oozie jobbkonfigurationen, använder du följande kommando:

    ```bash
    nano job.xml
    ```

3. När nanoredigeraren öppnas, använder du följande XML som innehållet i filen:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Ersätt alla förekomster av `wasb://mycontainer@mystorageaccount.blob.core.windows.net` med värdet som du fick tidigare för standardlagring.

     > [!WARNING]
     > Om sökvägen är en `wasb` sökväg, måste du använda den fullständiga sökvägen. Inte förkorta den så att den bara `wasb:///`.

   * Ersätt `YourName` med ditt inloggningsnamn för HDInsight-klustret.
   * Ersätt `serverName`, `adminLogin`, och `adminPassword` med informationen för din SQL-databas.

     De flesta av informationen i den här filen används för att fylla i de värden som används i workflow.xml eller ooziewf.hql filer, till exempel `${nameNode}`.

     > [!NOTE]
     > Den `oozie.wf.application.path` posten anger var du hittar filen workflow.xml. Den här filen innehåller arbetsflödet som körs som det här jobbet.

5. Om du vill spara filen, Välj Ctrl + X, ange `Y`, och välj sedan **RETUR**.

## <a name="submit-and-manage-the-job"></a>Skicka och hantera jobbet

Följande steg Använd Oozie-kommando för att skicka och hantera Oozie arbetsflöden på klustret. Kommandot Oozie finns ett eget användargränssnitt över den [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> När du använder kommandot Oozie, måste du använda det fullständiga Domännamnet för HDInsight-huvudnoden. Detta FQDN är endast tillgänglig från klustret, eller om klustret är i ett Azure-nätverk från andra datorer i samma nätverk.


1. Om du vill hämta URL: en till Oozie-tjänsten, använder du följande kommando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Detta returnerar information, till exempel följande XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Den `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` delen är URL som ska användas med kommandot Oozie.

2. Använd följande, för att skapa en miljövariabel för URL: en, så att du inte behöver ange den för alla kommandon:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Ersätt URL: en med den som du fick tidigare.
3. För att skicka jobbet, använder du följande:

    ```bash
    oozie job -config job.xml -submit
    ```

    Det här kommandot laddar jobbinformation från `job.xml` och skickar den till Oozie, men inte köra den.

    När kommandot har slutförts bör den returnera ID för jobbet, till exempel `0000005-150622124850154-oozie-oozi-W`. Detta ID används för att hantera jobbet.

4. Om du vill visa status för jobbet, använder du följande kommando:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Ersätt `<JOBID>` med det ID som returnerades i föregående steg.

    Detta returnerar information, till exempel följande text:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Det här jobbet har statusen `PREP`. Denna status anger att jobbet har skapats, men inte har startats.

5. Starta jobbet med följande kommando:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Ersätt `<JOBID>` med ID returneras tidigare.

    Om du kan kontrollera status för efter det här kommandot, den är i tillståndet och information returneras för åtgärder i jobbet.

6. När uppgiften har slutförts kan du verifiera att data har genererats och exporteras till SQL-databastabell med hjälp av följande kommando:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    På den `1>` uppmanar, anger du följande fråga:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Den information som returneras liknar följande text:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Läs mer om kommandot Oozie [Oozie kommandoradsverktyget](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST-API

Du kan skapa dina egna verktyg som fungerar med Oozie med Oozie REST API. Följande är HDInsight-specifik information om hur du använder för Oozie REST-API:

* **URI: N**: du kan komma åt REST-API från utanför klustret på `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autentisering**: Använd API: et för att autentisera, HTTP klusterkontot (admin) och lösenord. Exempel:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Läs mer om hur du använder REST-API Oozie [Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie-Webbgränssnittet

Oozie webbgränssnittet ger en webbaserad översikt över statusen för Oozie-jobb i klustret. Du kan visa följande information med webbgränssnitt:

   * Jobbstatus
   * Jobbdefinition
   * Konfiguration
   * Ett diagram över åtgärderna i jobbet
   * Loggar för jobbet

Du kan också visa information om åtgärderna i ett jobb.

Utför följande steg för att komma åt webbgränssnittet för Oozie:

1. Skapa en SSH-tunnel till HDInsight-kluster. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. När du skapar en tunnel kan du öppna Ambari-webbgränssnittet i webbläsaren. URI: N för webbplatsen Ambari är `https://CLUSTERNAME.azurehdinsight.net`. Ersätt `CLUSTERNAME` med namnet på ditt Linux-baserade HDInsight-kluster.

3. Till vänster på sidan Välj **Oozie** > **snabblänkar** > **Oozie-Webbgränssnittet**.

    ![Bild av menyerna](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Som standard visa arbetsflödesjobb som körs Oozie-webbgränssnittet. Om du vill se alla arbetsflödesjobb **alla jobb**.

    ![Alla jobb visas](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Välj jobbet för att visa mer information om ett jobb.

    ![Jobbinformation](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Från den **jobbinformation** fliken ser du den grundläggande jobbinformationen och de enskilda åtgärderna i jobbet. Du kan använda flikarna längst upp för att visa den **jobbdefinitionen**, **jobbkonfigurationen**, åtkomst till den **Jobblogg**, eller visa en riktad Acyklisk graf (DAG) för jobbet under **Jobb DAG**.

   * **Jobblogg**: Välj den **hämta loggar** för att hämta alla loggar för jobbet, eller använda den **ange sökfilter** fält som du vill filtrera loggarna.

       ![Jobblogg](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Jobbet DAG**: I DAG är en grafisk översikt över datasökvägar ledas genom arbetsflödet.

       ![Jobbet DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Om du väljer en av åtgärderna från den **jobbinformation** fliken den öppnar information för åtgärden. Till exempel välja den **RunSqoopExport** åtgärd.

    ![Åtgärd-info](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Du kan se information för åtgärden, till exempel en länk till den **-konsolens URL**. Använd den här länken om du vill visa spåraren jobbinformation för jobbet.

## <a name="schedule-jobs"></a>Schemalägga jobb

Du kan använda koordinatorn för att ange en start och slut förekomsten frekvens för jobb. För att definiera ett schema för arbetsflödet, gör du följande:

1. Använd följande kommando för att skapa en fil med namnet **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Använd följande XML som innehållet i filen:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]
    > Den `${...}` variabler ersätts med värden i jobbdefinitionen vid körning. Variablerna är:
    >
    > * `${coordFrequency}`: Tiden mellan instanser av jobbet körs.
    > * `${coordStart}`: Jobbets starttid.
    > * `${coordEnd}`: Jobbet sluttid.
    > * `${coordTimezone}`: Coordinator jobb finns i en fast tidszon med inga sommartid vanligtvis representeras med hjälp av UTC. Den här tidszonen kallas den *Oozie bearbetning tidszon.*
    > * `${wfPath}`: Sökvägen till workflow.xml.

2. Om du vill spara filen, Välj Ctrl + X, ange `Y`, och välj sedan **RETUR**.

3. Om du vill kopiera filen till arbetskatalogen för jobbets, använder du följande kommando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Att ändra den `job.xml` fil ska du använda följande kommando:

    ```
    nano job.xml
    ```

    Gör följande ändringar:

   * Om du vill instruera Oozie att köra coordinator-fil i stället för arbetsflödet, ändra `<name>oozie.wf.application.path</name>` till `<name>oozie.coord.application.path</name>`.

   * Ange den `workflowPath` variabeln som används av koordinatorn, Lägg till följande XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Ersätt den `wasb://mycontainer@mystorageaccount.blob.core.windows` text med det värde som används i andra poster i filen job.xml.

   * För att definiera start, slutet och frekvens för koordinatorn, lägger du till följande XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Dessa värden ange starttiden till 12:00 PM 10 maj 2017 och sluttid för den 12 maj 2017. Intervallet för att köra det här jobbet anges varje dag. Frekvensen är på några minuter, så 24 timmar x 60 minuter = 1 440 minuter. Slutligen är tidszonen inställd på UTC.

5. Om du vill spara filen, Välj Ctrl + X, ange `Y`, och välj sedan **RETUR**.

6. För att köra jobbet, använder du följande kommando:

    ```
    oozie job -config job.xml -run
    ```

    Det här kommandot skickar och startar jobbet.

7. Om du går till Oozie-webbgränssnitt och välj den **Coordinator jobb** fliken visas information som i följande bild:

    ![Fliken för Coordinator-jobb](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    Den **nästa Materialization** posten innehåller vid nästa tillfälle som jobbet körs.

8. Som tidigare arbetsflödesjobbet, om du väljer posten jobbet i webbläsaren visas information på jobbet:

    ![Coordinator jobbinformation](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Den här bilden visar endast lyckade körningar av jobbet, inte de enskilda åtgärderna inom det schemalagda arbetsflödet. Om du vill se de enskilda åtgärderna, väljer du något av de **åtgärd** poster.

    ![Åtgärd-info](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Felsökning

Med Oozie UI, kan du visa Oozie-loggar. Oozie UI innehåller också länkar till JobTracker loggarna för de MapReduce-uppgifter som startades av arbetsflödet. Mönstret för att felsöka bör vara:

   1. Visa jobbet i Oozie-Webbgränssnittet.

   2. Om det uppstår ett fel eller ett fel för en viss åtgärd, väljer du åtgärder för att se om den **felmeddelande** fältet finns mer information om felet.

   3. Om alternativet är tillgängligt kan du använda URL: en från åtgärden för att visa mer information, till exempel JobTracker-loggarna för åtgärden.

Följande är vissa fel kan uppstå och hur du löser dem.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Det går inte att initiera kluster

**Symptom**: jobbet har statusen ändras till **PAUSAD**. Information om jobbet visar den `RunHiveScript` status som **START_MANUAL**. Att välja åtgärden visas följande felmeddelande visas:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Orsak**: The Azure Blob storage-adresser som används i den **job.xml** filen innehåller inte storage-behållare eller lagringskontonamn. Formatet för Blob storage-adress måste vara `wasb://containername@storageaccountname.blob.core.windows.net`.

**Lösning**: ändra Blob storage-adresser som används av jobbet.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie är inte tillåtet att personifiera &lt;användare >

**Symptom**: jobbet har statusen ändras till **PAUSAD**. Information om jobbet visar den `RunHiveScript` status som **START_MANUAL**. Om du väljer åtgärden visas följande felmeddelande visas:

    JA002: User: oozie is not allowed to impersonate <USER>

**Orsak**: de aktuella behörighetsinställningarna Tillåt inte Oozie att personifiera det angivna användarkontot.

**Lösning**: Oozie kan personifiera användare i den **användare** grupp. Använd den `groups USERNAME` att se de grupper som användaren är medlem i. Om användaren inte är medlem i den **användare** gruppen, Använd följande kommando för att lägga till användaren i gruppen:

    sudo adduser USERNAME users

> [!NOTE]
> Det kan ta flera minuter innan HDInsight identifierar att användaren har lagts till i gruppen.

### <a name="launcher-error-sqoop"></a>Starta fel (Sqoop)

**Symptom**: jobbet har statusen ändras till **KILLED**. Information om jobbet visar den `RunSqoopExport` status som **fel**. Om du väljer åtgärden visas följande felmeddelande visas:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Orsak**: Sqoop kan inte läsa in databasdrivrutinen som krävs för att få åtkomst till databasen.

**Lösning**: när du använder Sqoop från ett Oozie-jobb kan du inkludera databasdrivrutinen med andra resurser, till exempel workflow.xml, jobbet använder. Dessutom referera till arkivet som innehåller databasdrivrutinen från den `<sqoop>...</sqoop>` delen av workflow.xml.

Till exempel för jobbet i det här dokumentet använder du följande steg:

1. Kopiera den `sqljdbc4.1.jar` filen till den **/självstudier/useoozie** directory:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Ändra den `workflow.xml` att lägga till följande XML-filen på en ny rad ovan `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du har lärt dig hur du definierar ett Oozie-arbetsflöde och hur du kör ett Oozie-jobb. Mer information om hur du arbetar med HDInsight finns i följande artiklar:

* [Använd tidsbaserade Oozie-koordinator med HDInsight][hdinsight-oozie-coordinator-time]
* [Överföra data för Hadoop-jobb i HDInsight][hdinsight-upload-data]
* [Använda Sqoop med Hadoop i HDInsight][hdinsight-use-sqoop]
* [Använda Hive med Hadoop i HDInsight][hdinsight-use-hive]
* [Använda Pig med Hadoop i HDInsight][hdinsight-use-pig]
* [Utveckla Java MapReduce-program för HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
