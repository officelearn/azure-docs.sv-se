---
title: Använd Hadoop Oozie-arbetsflöden i Linux-baserade Azure HDInsight
description: Använd Hadoop Oozie i Linux-baserade HDInsight. Läs om hur du definierar ett Oozie-arbetsflöde och skickar in ett Oozie-jobb.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: ece6fdb743035069bc6c666d6e90c76860f63e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744908"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Använda Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde på Azure HDInsight som körs på Linux

Läs om hur du använder Apache Oozie med Apache Hadoop på Azure HDInsight. Oozie är ett arbetsflöde och samordningssystem som hanterar Hadoop jobb. Oozie är integrerad med Hadoop stacken, och den stöder följande jobb:

* Apache Hadoop MapReduce
* Apache gris
* Apache Hive
* Apache Sqoop

Du kan också använda Oozie för att schemalägga jobb som är specifika för ett system, till exempel Java-program eller skalskript.

> [!NOTE]  
> Ett annat alternativ för att definiera arbetsflöden med HDInsight är att använda Azure Data Factory. Mer information om Data Factory finns i [Använda Apache Pig och Apache Hive med Data Factory][azure-data-factory-pig-hive]. Information om hur du använder Oozie i kluster med Enterprise Security Package finns [i Kör Apache Oozie i HDInsight Hadoop-kluster med Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Krav

* **Ett Hadoop-kluster på HDInsight**. Se [Komma igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **En SSH-klient**. Se [Anslut till HDInsight (Apache Hadoop) med SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **En Azure SQL-databas**.  Se [Skapa en Azure SQL-databas i Azure-portalen](../sql-database/sql-database-get-started.md).  Den här artikeln använder en databas som heter **oozietest**.

* [URI-schemat](./hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta skulle `wasb://` vara för `abfs://` Azure Storage, för `adl://` Azure Data Lake Storage Gen2 eller för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Azure `wasbs://`Storage, skulle URI vara . Se även [säker överföring](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Exempel på arbetsflöde

Arbetsflödet som används i det här dokumentet innehåller två åtgärder. Åtgärder är definitioner för uppgifter, till exempel att köra Hive, Sqoop, MapReduce eller andra processer:

![ARBETSFLÖDESDIAGRAM FÖR HDInsight oozie](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. En Hive-åtgärd kör ett HiveQL-skript för att extrahera poster från `hivesampletable` det som ingår i HDInsight. Varje rad med data beskriver ett besök från en viss mobil enhet. Postformatet visas som följande text:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Hive-skriptet som används i det här dokumentet räknar de totala besöken för varje plattform, till exempel Android eller iPhone, och lagrar antalet till ett nytt Hive-bord.

    Mer information om Hive finns i [Använda Apache Hive med HDInsight][hdinsight-use-hive].

2. En Sqoop-åtgärd exporterar innehållet i den nya Hive-tabellen till en tabell som skapats i Azure SQL Database. Mer information om Sqoop finns i [Använda Apache Sqoop med HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Oozie-versioner som stöds på HDInsight-kluster finns [i Nyheter i Hadoop-klusterversionerna från HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Skapa arbetskatalogen

Oozie förväntar sig att du lagrar alla resurser som krävs för ett jobb i samma katalog. I det `wasbs:///tutorials/useoozie`här exemplet används . Så här skapar du den här katalogen:

1. Redigera koden nedan `sshuser` för att ersätta med SSH-användarnamnet för klustret och ersätt `CLUSTERNAME` med namnet på klustret.  Ange sedan koden för att ansluta till HDInsight-klustret med hjälp av [SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Så här skapar du katalogen:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > Parametern `-p` gör att alla kataloger skapas i sökvägen. Katalogen `data` används för att lagra `useooziewf.hql` de data som används av skriptet.

3. Redigera koden nedan `sshuser` för att ersätta med ditt SSH-användarnamn.  Så här kontrollerar du att Oozie kan utge sig för att vara ditt användarkonto:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Du kan ignorera fel som indikerar att användaren `users` redan är medlem i gruppen.

## <a name="add-a-database-driver"></a>Lägga till en databasdrivrutin

Eftersom det här arbetsflödet använder Sqoop för att exportera data till SQL-databasen måste du tillhandahålla en kopia av JDBC-drivrutinen som används för att interagera med SQL-databasen. Om du vill kopiera JDBC-drivrutinen till arbetskatalogen använder du följande kommando från SSH-sessionen:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Verifiera den faktiska JDBC-drivrutin som finns på `/usr/share/java/`.

Om arbetsflödet använde andra resurser, till exempel en burk som innehåller ett MapReduce-program, måste du också lägga till dessa resurser.

## <a name="define-the-hive-query"></a>Definiera Hive-frågan

Använd följande steg för att skapa ett Hive-frågespråk (HiveQL) skript som definierar en fråga. Du ska använda frågan i ett Oozie-arbetsflöde senare i det här dokumentet.

1. Från SSH-anslutningen använder du följande kommando `useooziewf.hql`för att skapa en fil med namnet:

    ```bash
    nano useooziewf.hql
    ```

1. När GNU nano-redigeraren har öppnats använder du följande fråga som innehållet i filen:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Det finns två variabler som används i skriptet:

   * `${hiveTableName}`: Innehåller namnet på den tabell som ska skapas.

   * `${hiveDataFolder}`: Innehåller platsen för lagring av datafilerna för tabellen.

     Arbetsflödesdefinitionsfilen, workflow.xml i den här artikeln, skickar dessa värden till det här HiveQL-skriptet vid körning.

1. Om du vill spara filen markerar du **Ctrl+X**, anger **Y**och väljer sedan **Retur**.  

1. Använd följande kommando `useooziewf.hql` för `wasbs:///tutorials/useoozie/useooziewf.hql`att kopiera till:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Det här `useooziewf.hql` kommandot lagrar filen i DEN HDFS-kompatibla lagringen för klustret.

## <a name="define-the-workflow"></a>Definiera arbetsflödet

Oozie arbetsflödesdefinitioner är skrivna i Hadoop Process Definition Language (hPDL), som är ett XML-processdefinitionsspråk. Så här definierar du arbetsflödet:

1. Använd följande uttryck för att skapa och redigera en ny fil:

    ```bash
    nano workflow.xml
    ```

2. När nanoredigeraren har öppnats anger du följande XML som filinnehåll:

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

    Det finns två åtgärder som definierats i arbetsflödet:

   * `RunHiveScript`: Den här åtgärden är `useooziewf.hql` startåtgärden och kör Hive-skriptet.

   * `RunSqoopExport`: Den här åtgärden exporterar data som skapats från Hive-skriptet till en SQL-databas med hjälp av Sqoop. Den här åtgärden `RunHiveScript` körs bara om åtgärden lyckas.

     Arbetsflödet har flera poster, `${jobTracker}`till exempel . Du ersätter dessa transaktioner med de värden som du använder i jobbdefinitionen. Du skapar jobbdefinitionen senare i det här dokumentet.

     Notera också `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` posten i avsnittet Sqoop. Den här posten instruerar Oozie att göra det här arkivet tillgängligt för Sqoop när den här åtgärden körs.

3. Om du vill spara filen markerar du **Ctrl+X**, anger **Y**och väljer sedan **Retur**.  

4. Använd följande kommando för `workflow.xml` att `/tutorials/useoozie/workflow.xml`kopiera filen till:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Skapa en tabell

> [!NOTE]  
> Det finns många sätt att ansluta till SQL Database för att skapa en tabell. Följande steg använder [FreeTDS](https://www.freetds.org/) från HDInsight-klustret.

1. Använd följande kommando för att installera FreeTDS i HDInsight-klustret:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Redigera koden nedan `<serverName>` för att ersätta med `<sqlLogin>` ditt Azure SQL-servernamn och med Azure SQL-serverinloggningen.  Ange kommandot för att ansluta till den nödvändiga SQL-databasen.  Ange lösenordet vid prompten.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Du får utdata som följande text:

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

    När instruktionen `GO` har angivits värderas de föregående instruktionerna. Dessa satser skapar `mobiledata`en tabell med namnet , som används av arbetsflödet.

    Om du vill kontrollera att tabellen har skapats använder du följande kommandon:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Du ser utdata som följande text:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Avsluta tsql-verktyget genom `exit` att `1>` gå in vid prompten.

## <a name="create-the-job-definition"></a>Skapa jobbdefinitionen

Jobbdefinitionen beskriver var du hittar arbetsflödet.xml. Den beskriver också var du hittar andra filer `useooziewf.hql`som används av arbetsflödet, till exempel . Dessutom definieras värdena för egenskaper som används i arbetsflödet och tillhörande filer.

1. Använd följande kommando för att få den fullständiga adressen till standardlagringen. Den här adressen används i konfigurationsfilen som du skapar i nästa steg.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Det här kommandot returnerar information som följande XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Om HDInsight-klustret använder Azure Storage `<value>` som standardlagring börjar elementinnehållet med `wasbs://`. Om Azure Data Lake Storage Gen1 används `adl://`i stället börjar den med . Om Azure Data Lake Storage Gen2 används `abfs://`börjar den med .

    Spara innehållet i `<value>` elementet, som det används i nästa steg.

2. Redigera xml nedan enligt följande:

    |Platshållarvärde| Ersatt värde|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Värde som erhållits från steg 1.|
    |admin| Ditt inloggningsnamn för HDInsight-klustret om inte admin.|
    |Servernamn| Azure SQL-databasservernamn.|
    |sqlLogin (på ett sätt)| Inloggning av Azure SQL-databasserver.|
    |sqlPassword (sqlPassword)| Inloggningslösenord för Azure SQL-databasserver.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
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
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    Det mesta av informationen i den här filen används för att fylla i de värden som `${nameNode}`används i filerna workflow.xml eller ooziewf.hql, till exempel .  Om banan är `wasbs` en bana måste du använda hela sökvägen. Förkorta det inte till `wasbs:///`bara. Posten `oozie.wf.application.path` definierar var filen workflow.xml ska hittas. Den här filen innehåller arbetsflödet som kördes av det här jobbet.

3. Om du vill skapa Oozie-jobbdefinitionskonfigurationen använder du följande kommando:

    ```bash
    nano job.xml
    ```

4. När nanoredigeraren har öppnats klistrar du in den redigerade XML:n som innehållet i filen.

5. Om du vill spara filen markerar du **Ctrl+X**, anger **Y**och väljer sedan **Retur**.

## <a name="submit-and-manage-the-job"></a>Skicka in och hantera jobbet

I följande steg används kommandot Oozie för att skicka in och hantera Oozie-arbetsflöden i klustret. Oozie kommandot är ett användarvänligt gränssnitt över [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> När du använder kommandot Oozie måste du använda FQDN för HDInsight-huvudnoden. Det här FQDN är endast tillgängligt från klustret, eller om klustret finns i ett virtuellt Azure-nätverk, från andra datorer i samma nätverk.

1. Om du vill hämta webbadressen till Oozie-tjänsten använder du följande kommando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Detta returnerar information som följande XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Den `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` del är den URL som ska användas med kommandot Oozie.

2. Redigera koden för att ersätta webbadressen med den du fick tidigare. Om du vill skapa en miljövariabel för URL:en använder du följande, så att du inte behöver ange den för varje kommando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Om du vill skicka jobbet använder du följande:

    ```bash
    oozie job -config job.xml -submit
    ```

    Det här kommandot läser `job.xml` in jobbinformationen från och skickar den till Oozie, men kör den inte.

    När kommandot är klart ska det returnera jobbets ID, till exempel `0000005-150622124850154-oozie-oozi-W`. Det här ID:t används för att hantera jobbet.

4. Redigera koden nedan `<JOBID>` för att ersätta med ID som returnerades i föregående steg.  Om du vill visa jobbets status använder du följande kommando:

    ```bash
    oozie job -info <JOBID>
    ```

    Detta returnerar information som följande text:

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

    Det här jobbet `PREP`har statusen . Den här statusen anger att jobbet har skapats, men inte startats.

5. Redigera koden nedan `<JOBID>` för att ersätta med ID som returnerats tidigare.  Starta jobbet genom att använda följande kommando:

    ```bash
    oozie job -start <JOBID>
    ```

    Om du kontrollerar status efter det här kommandot är det i körläge och information returneras för åtgärderna i jobbet.  Jobbet tar några minuter att slutföra.

6. Redigera koden nedan `<serverName>` för att ersätta med `<sqlLogin>` ditt Azure SQL-servernamn och med Azure SQL-serverinloggningen.  *När aktiviteten har slutförts* kan du kontrollera att data har genererats och exporterats till SQL-databastabellen med hjälp av följande kommando.  Ange lösenordet vid prompten.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Ange `1>` följande fråga vid prompten:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Informationen som returneras är som följande text:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Mer information om kommandot Oozie finns i [Kommandoradsverktyget Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Med Oozie REST API kan du bygga dina egna verktyg som fungerar med Oozie. Följande är HDInsight-specifik information om användningen av Oozie REST API:

* **URI**: Du kan komma åt REST `https://CLUSTERNAME.azurehdinsight.net/oozie`API från utanför klustret på .

* **Autentisering**: Om du vill autentisera använder du API:et i klustret HTTP-kontot (admin) och lösenordet. Ett exempel:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Mer information om hur du använder Oozie REST API finns i [Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie webb UI

Oozie webbgränssnitt ger en webbaserad vy över status för Oozie-jobb i klustret. Med webbgränssnittet kan du visa följande information:

   * Jobbstatus
   * Jobbdefinition
   * Konfiguration
   * Ett diagram över åtgärderna i jobbet
   * Loggar för jobbet

Du kan också visa information om åtgärderna i ett jobb.

Så här öppnar du webbgränssnittet Oozie:

1. Skapa en SSH-tunnel till HDInsight-klustret. Mer information finns i [Använda SSH-tunnel med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. När du har skapat en tunnel öppnar du webbgränssnittet Ambari i webbläsaren med URI `http://headnodehost:8080`.

3. Från vänster sida av sidan väljer du **Oozie** > **Quick Links** > **Oozie Web UI**.

    ![Apache Ambari oozie web ui steg](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Oozie-webbgränssnittet kan som standard visa arbetsflödesjobb som körs. Om du vill visa alla arbetsflödesjobb väljer du **Alla jobb**.

    ![Oozie webbkonsol arbetsflöde jobb](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Om du vill visa mer information om ett jobb väljer du jobbet.

    ![HDInsight Apache Oozie jobb info](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. På fliken **Jobbinformation** kan du se grundläggande jobbinformation och enskilda åtgärder i jobbet. Du kan använda flikarna högst upp för att visa **jobbdefinitionen,** **jobbkonfigurationen**, komma åt **jobbloggen**eller visa ett riktat acykliskt diagram (DAG) för jobbet under **Jobb-DAG**.

   * **Jobblogg:** Välj knappen **Hämta loggar** för att hämta alla loggar för jobbet, eller använd fältet **Ange sökfilter** för att filtrera loggarna.

       ![HDInsight Apache Oozie jobblogg](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **JobbDAG:** DAGEN är en grafisk översikt över de datasökvägar som tas via arbetsflödet.

       ![HDInsight Apache Oozie jobb dag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Om du väljer en av åtgärderna på fliken **Jobbinformation** visas information om åtgärden. Välj till exempel åtgärden **RunSqoopExport.**

    ![HDInsight oozie jobb åtgärd info](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Du kan se information om åtgärden, till exempel en länk till **konsolens URL**. Använd den här länken om du vill visa jobbspårningsinformation för jobbet.

## <a name="schedule-jobs"></a>Schemalägga jobb

Du kan använda koordinatorn för att ange en start, ett och förekomstfrekvensen för jobb. Så här definierar du ett schema för arbetsflödet:

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
    > Variablerna `${...}` ersätts med värden i jobbdefinitionen vid körning. Variablerna är:
    >
    > * `${coordFrequency}`: Tiden mellan att köra instanser av jobbet.
    > * `${coordStart}`: Jobbets starttid.
    > * `${coordEnd}`: Jobbets sluttid.
    > * `${coordTimezone}`: Koordinatjobb finns i en fast tidszon utan sommartid, som vanligtvis representeras med hjälp av UTC. Denna tidszon kallas *Oozie bearbetning tidszon.*
    > * `${wfPath}`: Sökvägen till workflow.xml.

2. Om du vill spara filen markerar du **Ctrl+X**, anger **Y**och väljer sedan **Retur**.

3. Om du vill kopiera filen till arbetskatalogen för det här jobbet använder du följande kommando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Om du `job.xml` vill ändra filen som du skapade tidigare använder du följande kommando:

    ```bash
    nano job.xml
    ```

    Gör följande ändringar:

   * Om du vill instruera Oozie att köra `<name>oozie.wf.application.path</name>` koordinatorn i stället för arbetsflödet ändrar du till `<name>oozie.coord.application.path</name>`.

   * Om du `workflowPath` vill ange den variabel som används av koordinatorn lägger du till följande XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Ersätt `wasbs://mycontainer@mystorageaccount.blob.core.windows` texten med värdet som används i de andra posterna i filen job.xml.

   * Om du vill definiera start- och slut- och frekvens för koordinatorn lägger du till följande XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
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

       Dessa värden anger starttiden till 12:00 den 10 maj 2018 och sluttiden till 12 maj 2018. Intervallet för att köra det här jobbet är inställt på dagligen. Frekvensen är i minuter, så 24 timmar x 60 minuter = 1440 minuter. Slutligen är tidszonen inställd på UTC.

5. Om du vill spara filen markerar du **Ctrl+X**, anger **Y**och väljer sedan **Retur**.

6. Om du vill skicka och starta jobbet använder du följande kommando:

    ```bash
    oozie job -config job.xml -run
    ```

7. Om du går till Oozie webbgränssnittet och väljer fliken **Samordna jobb,** ser du information som i följande bild:

    ![Oozie webbkonsken samordnare jobb fliken](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    **Posten Nästa materialisering** innehåller nästa gång jobbet körs.

8. I likhet med det tidigare arbetsflödesjobbet visas information om jobbet om du väljer jobbposten i webbgränssnittet:

    ![Apache Oozie samordnare jobb info](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Den här bilden visar bara lyckade körningar av jobbet, inte de enskilda åtgärderna i det schemalagda arbetsflödet. Om du vill visa de enskilda åtgärderna väljer du en av **åtgärdsposterna.**

    ![OOzie webbkonsol jobb info fliken](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Felsökning

Med Oozie UI kan du visa Oozie-loggar. Oozie-användargränssnittet innehåller också länkar till JobTracker-loggarna för MapReduce-uppgifter som startades av arbetsflödet. Mönstret för felsökning bör vara:

   1. Visa jobbet i Oozie webbgränssnitt.

   2. Om det finns ett fel eller ett fel för en viss åtgärd väljer du åtgärden för att se om fältet **Felmeddelande** innehåller mer information om felet.

   3. Om det är tillgängligt använder du URL:en från åtgärden för att visa mer information, till exempel JobTracker-loggarna, för åtgärden.

Följande är specifika fel som kan uppstå och hur du löser dem.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Det går inte att initiera klustret

**Symptom**: Jobbstatusen ändras till **SUSPENDERAD**. Information om jobbet `RunHiveScript` visar statusen **som START_MANUAL**. Om du väljer åtgärden visas följande felmeddelande:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Orsak**: Azure Blob-lagringsadresserna som används i **job.xml-filen** innehåller inte lagringsbehållaren eller lagringskontonamnet. Blob-lagringsadressformatet `wasbs://containername@storageaccountname.blob.core.windows.net`måste vara .

**Lösning**: Ändra de Blob-lagringsadresser som jobbet använder.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie får inte &lt;utge sig för att vara ANVÄNDARE&gt;

**Symptom**: Jobbstatusen ändras till **SUSPENDERAD**. Information om jobbet `RunHiveScript` visar statusen **som START_MANUAL**. Om du väljer åtgärden visas följande felmeddelande:

    JA002: User: oozie is not allowed to impersonate <USER>

**Orsak**: De aktuella behörighetsinställningarna tillåter inte att Oozie personifierar det angivna användarkontot.

**Lösning**: Oozie kan personifiera användare i **användargruppen.** Använd `groups USERNAME` för att se de grupper som användarkontot är medlem i. Om användaren inte är medlem i **användargruppen** använder du följande kommando för att lägga till användaren i gruppen:

    sudo adduser USERNAME users

> [!NOTE]  
> Det kan ta flera minuter innan HDInsight erkänner att användaren har lagts till i gruppen.

### <a name="launcher-error-sqoop"></a>Startfel (Sqoop)

**Symptom**: Jobbstatusen ändras till **KILLED**. Information för jobbet `RunSqoopExport` visar statusen som **FEL**. Om du väljer åtgärden visas följande felmeddelande:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Orsak:** Sqoop kan inte läsa in databasdrivrutinen som krävs för att komma åt databasen.

**Lösning**: När du använder Sqoop från ett Oozie-jobb måste du inkludera databasdrivrutinen med andra resurser, till exempel workflow.xml, som jobbet använder. Referera också till arkivet som innehåller `<sqoop>...</sqoop>` databasdrivrutinen från avsnittet i workflow.xml.

För jobbet i det här dokumentet kan du till exempel använda följande steg:

1. Kopiera `mssql-jdbc-7.0.0.jre8.jar` filen till **katalogen /tutorials/useoozie:**

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Ändra `workflow.xml` om du vill lägga till `</sqoop>`följande XML på en ny rad ovan:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att definiera ett Oozie-arbetsflöde och hur du kör ett Oozie-jobb. Mer information om hur du arbetar med HDInsight finns i följande artiklar:

* [Ladda upp data för Apache Hadoop-jobb i HDInsight][hdinsight-upload-data]
* [Använd Apache Sqoop med Apache Hadoop i HDInsight][hdinsight-use-sqoop]
* [Använd Apache Hive med Apache Hadoop på HDInsight][hdinsight-use-hive]
* [Utveckla Java MapReduce-program för HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
