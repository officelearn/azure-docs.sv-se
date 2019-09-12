---
title: Använda Hadoop Oozie-arbetsflöden i Linux-baserade Azure HDInsight
description: Använda Hadoop-Oozie i Linux-baserade HDInsight. Lär dig hur du definierar ett Oozie-arbetsflöde och skickar ett Oozie-jobb.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d601dc1efe8dc3f6f2678f5d4df03f172146cd07
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900465"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Använd Apache Oozie med Apache Hadoop för att definiera och köra ett arbets flöde på Linux-baserade Azure HDInsight

Lär dig hur du använder Apache Oozie med Apache Hadoop på Azure HDInsight. Oozie är ett arbets flödes-och samordnings system som hanterar Hadoop-jobb. Oozie är integrerat med Hadoop-stacken och stöder följande jobb:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Du kan också använda Oozie för att schemalägga jobb som är speciella för ett system, t. ex. Java-program eller Shell-skript.

> [!NOTE]  
> Ett annat alternativ för att definiera arbets flöden med HDInsight är att använda Azure Data Factory. Läs mer om Data Factory i [använda Apache gris och Apache Hive med Data Factory][azure-data-factory-pig-hive]. Om du vill använda Oozie i kluster med Enterprise Security Package kan du läsa [köra apache Oozie i HDInsight Hadoop-kluster med Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).


## <a name="prerequisites"></a>Förutsättningar

* **Ett Hadoop-kluster i HDInsight**. Se [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **En SSH-klient**. Se [ansluta till HDInsight (Apache Hadoop) med SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **En Azure SQL Database**.  Se [skapa en Azure SQL-databas i Azure Portal](../sql-database/sql-database-get-started.md).  I den här artikeln används en `oozietest`databas med namnet.

* [URI-schemat](./hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta gäller Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage gen1. `wasb://` Om säker överföring har Aktiver ATS för Azure Storage eller data Lake Storage Gen2, skulle URI `wasbs://` : `abfss://`n vara eller, se även [säker överföring](../storage/common/storage-require-secure-transfer.md).


## <a name="example-workflow"></a>Exempel arbets flöde

Arbets flödet som används i det här dokumentet innehåller två åtgärder. Åtgärder är definitioner för aktiviteter, till exempel körning av Hive, Sqoop, MapReduce eller andra processer:

![Arbets flödes diagram][img-workflow-diagram]

1. En Hive-åtgärd kör ett HiveQL-skript för att extrahera `hivesampletable` poster från det som ingår i HDInsight. Varje datarad beskriver ett besök från en speciell mobil enhet. Post formatet visas som följande text:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Hive-skriptet som används i det här dokumentet räknar antalet besök för varje plattform, till exempel Android eller iPhone, och lagrar antalet i en ny Hive-tabell.

    Mer information om Hive finns i [använda Apache Hive med HDInsight][hdinsight-use-hive].

2. En Sqoop-åtgärd exporterar innehållet i den nya Hive-tabellen till en tabell som skapats i Azure SQL Database. Mer information om Sqoop finns i [använda Apache Sqoop med HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Information om vilka Oozie-versioner som stöds i HDInsight-kluster finns i [Nyheter i de Hadoop-kluster versioner som tillhandahålls av HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Skapa arbets katalogen

Oozie förväntar dig att lagra alla resurser som krävs för ett jobb i samma katalog. I det här `wasbs:///tutorials/useoozie`exemplet används. Utför följande steg för att skapa den här katalogen:

1. Redigera koden nedan och Ersätt `sshuser` med SSH-användarnamnet för klustret och Ersätt `clustername` med namnet på klustret.  Ange sedan koden för att ansluta till HDInsight-klustret med [hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

2. Använd följande kommando för att skapa katalogen:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > `-p` Parametern gör att alla kataloger i sökvägen skapas. Katalogen används för att lagra de data som används `useooziewf.hql` av skriptet. `data`

3. Redigera koden nedan och Ersätt `username` med ditt SSH-användarnamn.  Använd följande kommando för att se till att Oozie kan personifiera ditt användar konto:

    ```bash
    sudo adduser username users
    ```

    > [!NOTE]  
    > Du kan ignorera fel som anger att användaren redan är medlem i `users` gruppen.

## <a name="add-a-database-driver"></a>Lägg till en databas driv rutin

Eftersom det här arbets flödet använder Sqoop för att exportera data till SQL-databasen, måste du ange en kopia av JDBC-drivrutinen som används för att interagera med SQL-databasen. Om du vill kopiera JDBC-drivrutinen till arbets katalogen använder du följande kommando från SSH-sessionen:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Kontrol lera den faktiska JDBC-drivrutinen som `/usr/share/java/`finns på.

Om arbets flödet har använt andra resurser, till exempel en jar-app som innehåller ett MapReduce-program, måste du även lägga till dessa resurser.

## <a name="define-the-hive-query"></a>Definiera Hive-frågan

Använd följande steg för att skapa ett HiveQL-skript (Hive Query Language) som definierar en fråga. Du kommer att använda frågan i ett Oozie-arbetsflöde senare i det här dokumentet.

1. Från SSH-anslutningen använder du följande kommando för att skapa en fil med `useooziewf.hql`namnet:

    ```bash
    nano useooziewf.hql
    ```

3. När GNU nano-redigeraren öppnas använder du följande fråga som filens innehåll:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Det finns två variabler som används i skriptet:

   * `${hiveTableName}`: Innehåller namnet på den tabell som ska skapas.

   * `${hiveDataFolder}`: Innehåller den plats där datafilerna för tabellen ska lagras.

     Arbets flödes definitions filen, Workflow. xml i den här artikeln, skickar dessa värden till det här HiveQL-skriptet vid körning.

4. Om du vill spara filen väljer du Ctrl + X, `Y`anger och väljer sedan **RETUR**.  

5. Använd följande kommando för att kopiera `useooziewf.hql` till `wasbs:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Det här kommandot sparar `useooziewf.hql` filen i det HDFS-kompatibla lagrings utrymmet för klustret.

## <a name="define-the-workflow"></a>Definiera arbets flödet

Oozie för arbets flödes definitioner skrivs i hPDL (Hadoop process Definition Language), vilket är ett XML-process definitions språk. Använd följande steg för att definiera arbets flödet:

1. Använd följande instruktion för att skapa och redigera en ny fil:

    ```bash
    nano workflow.xml
    ```

2. När nano-redigeraren öppnas anger du följande XML som fil innehåll:

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

    Det finns två åtgärder definierade i arbets flödet:

   * `RunHiveScript`: Den här åtgärden är start åtgärden och kör `useooziewf.hql` Hive-skriptet.

   * `RunSqoopExport`: Den här åtgärden exporterar data som skapats från Hive-skriptet till en SQL-databas med hjälp av Sqoop. Den här åtgärden körs bara om `RunHiveScript` åtgärden lyckas.

     Arbets flödet har flera poster, till exempel `${jobTracker}`. Du ersätter dessa poster med de värden som du använder i jobb definitionen. Du kommer att skapa jobb definitionen senare i det här dokumentet.

     Observera `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` också posten i avsnittet Sqoop. Den här posten instruerar Oozie att göra det här arkivet tillgängligt för Sqoop när den här åtgärden körs.

3. Om du vill spara filen väljer du Ctrl + X, `Y`anger och väljer sedan **RETUR**.  

4. Använd följande kommando för att kopiera `workflow.xml` filen till: `/tutorials/useoozie/workflow.xml`

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

2. Redigera koden nedan för att ersätta `<serverName>` med ditt Azure SQL Server-namn och `<sqlLogin>` med Azure SQL Server-inloggningen.  Ange kommandot för att ansluta till den nödvändiga SQL-databasen.  Ange lösen ordet vid prompten.

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

    När instruktionen `GO` har angivits värderas de föregående instruktionerna. Dessa instruktioner skapar en tabell med namnet `mobiledata`, som används av arbets flödet.

    Kontrol lera att tabellen har skapats genom att använda följande kommandon:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Du ser utdata som följande text:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Avsluta tsql-verktyget genom att `exit` ange `1>` i prompten.

## <a name="create-the-job-definition"></a>Skapa jobb definitionen

Jobb definitionen beskriver var du hittar Workflow. xml. Den beskriver också var du hittar andra filer som används av arbets flödet, till `useooziewf.hql`exempel. Dessutom definierar den värden för egenskaper som används i arbets flödet och de associerade filerna.

1. Använd följande kommando för att få en fullständig adress till standard lagrings utrymmet. Den här adressen används i konfigurations filen som du skapar i nästa steg.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Det här kommandot returnerar information som till exempel följande XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Om HDInsight- `<value>` klustret använder Azure Storage som standard lagring börjar element innehållet med `wasbs://`. Om Azure Data Lake Storage Gen1 används i stället börjar det med `adl://`. Om Azure Data Lake Storage Gen2 används börjar det med `abfs://`.

    Spara innehållet i `<value>` elementet som det används i nästa steg.

2. Redigera XML-koden nedan enligt följande:

    |Plats hållarens värde| Ersatt värde|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Värdet togs emot från steg 1.|
    |innehavaradministration| Ditt inloggnings namn för HDInsight-klustret om det inte är administratör.|
    |Servernamn| Namn på Azure SQL Database-Server.|
    |sqlLogin| Inloggning av Azure SQL Database-Server.|
    |sqlPassword| Inloggnings lösen ord för Azure SQL Database-servern.|

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

    Merparten av informationen i den här filen används för att fylla i de värden som används i ooziewf. XML-eller. HQL-filer `${nameNode}`, till exempel.  Om sökvägen är en `wasbs` sökväg måste du använda den fullständiga sökvägen. Förkorta det inte till bara `wasbs:///`. `oozie.wf.application.path` Posten definierar var du hittar filen Workflow. xml. Den här filen innehåller det arbets flöde som kördes av det här jobbet.

3. Om du vill skapa jobb definitions konfigurationen för Oozie använder du följande kommando:

    ```bash
    nano job.xml
    ```

4. När nano-redigeraren öppnas klistrar du in den redigerade XML-filen som innehållet i filen.

5. Om du vill spara filen väljer du Ctrl + X, `Y`anger och väljer sedan **RETUR**.

## <a name="submit-and-manage-the-job"></a>Skicka och hantera jobbet

I följande steg används kommandot Oozie för att skicka och hantera Oozie-arbetsflöden i klustret. Oozie-kommandot är ett användarvänligt gränssnitt över [Oozie-REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> När du använder kommandot Oozie måste du använda det fullständiga domän namnet för HDInsight Head-noden. Detta fullständiga domän namn är endast tillgängligt från klustret, eller om klustret finns i ett virtuellt Azure-nätverk, från andra datorer i samma nätverk.

1. Använd följande kommando för att hämta URL: en till Oozie-tjänsten:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Detta returnerar information som till exempel följande XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` Delen är den URL som ska användas med kommandot Oozie.

2. Redigera koden för att ersätta URL: en med den som du fick tidigare. Om du vill skapa en miljö variabel för URL: en använder du följande, så att du inte behöver ange den för varje kommando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Använd följande för att skicka jobbet:

    ```bash
    oozie job -config job.xml -submit
    ```

    Det här kommandot läser in jobb informationen `job.xml` från och skickar den till Oozie, men kör den inte.

    När kommandot har slutförts ska det returnera jobbets ID, till exempel `0000005-150622124850154-oozie-oozi-W`. Detta ID används för att hantera jobbet.

4. Redigera koden nedan för att ersätta `<JOBID>` med det ID som returnerades i föregående steg.  Använd följande kommando för att Visa jobbets status:

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

    Det här jobbet har statusen `PREP`. Den här statusen indikerar att jobbet skapades, men inte startades.

5. Redigera koden nedan för att ersätta `<JOBID>` med det ID som returnerades tidigare.  Starta jobbet med följande kommando:

    ```bash
    oozie job -start <JOBID>
    ```

    Om du kontrollerar status efter det här kommandot är det i ett körnings tillstånd och information returneras för åtgärderna i jobbet.  Det tar några minuter att slutföra jobbet.

6. Redigera koden nedan för att ersätta `<serverName>` med ditt Azure SQL Server-namn och `<sqlLogin>` med Azure SQL Server-inloggningen.  När aktiviteten har slutförts kan du kontrol lera att data har genererats och exporter ATS till SQL Database-tabellen med hjälp av följande kommando.  Ange lösen ordet vid prompten.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Ange följande fråga i prompten:`1>`

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

Mer information om Oozie-kommandot finns i [kommando rads verktyget Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Med Oozie-REST API kan du bygga egna verktyg som fungerar med Oozie. Följande är HDInsight-speciell information om användningen av Oozie-REST API:

* **URI**: Du kan komma åt REST API utanför klustret på `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autentisering**: Autentisera genom att använda API: t för klustrets kluster-HTTP-konto (admin) och lösen ord. Exempel:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Mer information om hur du använder Oozie-REST API finns i [Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie webb gränssnitt

Oozie-webbgränssnittet innehåller en webbaserad vy över statusen för Oozie-jobb i klustret. Med webb gränssnittet kan du visa följande information:

   * Jobbstatus
   * Jobbdefinition
   * Konfiguration
   * Ett diagram över åtgärder i jobbet
   * Loggar för jobbet

Du kan också visa information om åtgärderna i ett jobb.

Utför följande steg för att få åtkomst till Oozie-webbgränssnittet:

1. Skapa en SSH-tunnel till HDInsight-klustret. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. När du har skapat en tunnel öppnar du Ambari-webbgränssnittet i webbläsaren med hjälp `http://headnodehost:8080`av URI.

3. Från vänster sida av sidan väljer du **Oozie** > **Quick Links** > **Oozie Web UI**.

    ![Bild av menyerna](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Oozie webb GRÄNSSNITTets standardinställningar visar de arbets flödes jobb som körs. Om du vill se alla arbets flödes jobb väljer du **alla jobb**.

    ![Alla jobb som visas](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Om du vill visa mer information om ett jobb väljer du jobbet.

    ![Jobbinformation](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. På fliken **jobb information** kan du se grundläggande jobb information och enskilda åtgärder i jobbet. Du kan använda flikarna längst upp för att visa **jobb definitionen**, **jobb konfigurationen**, komma åt **jobb loggen**eller visa ett dirigerat acykliska diagram (DAG) för jobbet under **jobb dag**.

   * **Jobb logg**: Välj knappen **Hämta loggar** för att hämta alla loggar för jobbet eller Använd fältet **Ange Sök filter** för att filtrera loggarna.

       ![Jobb logg](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Jobb dag**: DAG är en grafisk översikt över data Sök vägar som tas i arbets flödet.

       ![Jobb DAG](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Om du väljer någon av åtgärderna på fliken **jobb information** visas information för åtgärden. Välj till exempel åtgärden **RunSqoopExport** .

    ![Åtgärds information](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Du kan se information om åtgärden, till exempel en länk till konsol- **URL:** en. Använd den här länken om du vill visa information om jobb Spårare för jobbet.

## <a name="schedule-jobs"></a>Schemalägga jobb

Du kan använda koordinatorn för att ange en start, en slut punkt och förekomst frekvens för jobb. Utför följande steg för att definiera ett schema för arbets flödet:

1. Använd följande kommando för att skapa en fil med namnet **koordinator. XML**:

    ```bash
    nano coordinator.xml
    ```

    Använd följande XML som filens innehåll:

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
    > `${...}` Variablerna ersätts av värden i jobb definitionen vid körning. Variablerna är:
    >
    > * `${coordFrequency}`: Tiden mellan aktiva instanser av jobbet.
    > * `${coordStart}`: Jobbets start tid.
    > * `${coordEnd}`: Jobbets slut tid.
    > * `${coordTimezone}`: Koordinator jobb finns i en fast tidszon utan sommar tid, vanligt vis representeras med hjälp av UTC. Den här tids zonen kallas för den *Oozie bearbetnings tids zonen.*
    > * `${wfPath}`: Sökvägen till arbets flödes. xml.

2. Om du vill spara filen väljer du Ctrl + X, `Y`anger och väljer sedan **RETUR**.

3. Om du vill kopiera filen till arbets katalogen för det här jobbet använder du följande kommando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Om du vill `job.xml` ändra filen som du skapade tidigare använder du följande kommando:

    ```bash
    nano job.xml
    ```

    Gör följande ändringar:

   * Om du vill instruera Oozie att köra koordinator filen i stället för arbets flödet `<name>oozie.wf.application.path</name>` ändrar `<name>oozie.coord.application.path</name>`du till.

   * Lägg till följande `workflowPath` XML om du vill ange variabeln som används av koordinatorn:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       `wasbs://mycontainer@mystorageaccount.blob.core.windows` Ersätt texten med det värde som används i de andra posterna i Job. XML-filen.

   * Om du vill definiera Start, slut och frekvens för koordinatorn lägger du till följande XML:

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

       Dessa värden anger start tiden till 12:00 PM den 10 maj 2018 och slut tiden till 12 maj 2018. Intervallet för att köra det här jobbet är inställt på varje dag. Frekvensen är i minuter, så 24 timmar x 60 minuter = 1440 minuter. Slutligen är tids zonen inställd på UTC.

5. Om du vill spara filen väljer du Ctrl + X, `Y`anger och väljer sedan **RETUR**.

6. Om du vill skicka och starta jobbet använder du följande kommando:

    ```bash
    oozie job -config job.xml -run
    ```

7. Om du går till Oozie-webbgränssnittet och väljer fliken **koordinator jobb** ser du information som i följande bild:

    ![Fliken koordinator jobb](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    **Nästa materialization** -post innehåller nästa gång som jobbet körs.

8. Precis som det tidigare arbets flödes jobbet, om du väljer jobb posten i webb gränssnittet, visas information om jobbet:

    ![Information om koordinator jobb](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > I den här bilden visas endast lyckade körningar av jobbet, inte de enskilda åtgärderna i det schemalagda arbets flödet. Välj en av **Åtgärds** posterna för att se de enskilda åtgärderna.

    ![Information om koordinator åtgärd](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Felsökning

Med Oozie-ANVÄNDARGRÄNSSNITTET kan du Visa Oozie-loggar. Oozie-ANVÄNDARGRÄNSSNITTET innehåller också länkar till JobTracker-loggarna för de MapReduce-uppgifter som startades av arbets flödet. Mönstret för fel sökning bör vara:

   1. Visa jobbet i Oozie-webbgränssnittet.

   2. Om det uppstår ett fel eller fel för en speciell åtgärd väljer du åtgärden för att se om **fel meddelande** fältet innehåller mer information om felet.

   3. Om det är tillgängligt använder du URL: en från åtgärden för att visa mer information, till exempel JobTracker-loggar, för åtgärden.

Följande är vissa fel kan uppstå och hur du löser dem.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Det går inte att initiera kluster

**Symptom**: Jobbets status ändras till **inaktive**rad. Information för jobbet visar `RunHiveScript` status som **START_MANUAL**. När du väljer åtgärden visas följande fel meddelande:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Orsak**: De Azure Blob Storage-adresser som används i **Job. XML-** filen innehåller inte lagrings containern eller lagrings konto namnet. Blob Storage-formatet måste vara `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Lösning**: Ändra de Blob Storage-adresser som används av jobbet.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie tillåts inte personifiera &lt;användare&gt;

**Symptom**: Jobbets status ändras till **inaktive**rad. Information för jobbet visar `RunHiveScript` status som **START_MANUAL**. Om du väljer åtgärden visas följande fel meddelande:

    JA002: User: oozie is not allowed to impersonate <USER>

**Orsak**: De aktuella behörighets inställningarna tillåter inte att Oozie personifierar det angivna användar kontot.

**Lösning**: Oozie kan personifiera användare i gruppen **användare** . `groups USERNAME` Använd för att se de grupper som användar kontot är medlem i. Om användaren inte är medlem i gruppen **användare** använder du följande kommando för att lägga till användaren i gruppen:

    sudo adduser USERNAME users

> [!NOTE]  
> Det kan ta flera minuter innan HDInsight känner av att användaren har lagts till i gruppen.

### <a name="launcher-error-sqoop"></a>Start fel (Sqoop)

**Symptom**: Jobbets status ändras till **avlivat**. Information för jobbet visar `RunSqoopExport` status som **fel**. Om du väljer åtgärden visas följande fel meddelande:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Orsak**: Sqoop kan inte läsa in den databas driv rutin som krävs för att komma åt databasen.

**Lösning**: När du använder Sqoop från ett Oozie-jobb måste du inkludera databas driv rutinen med de andra resurserna, t. ex. Workflow. XML, som används av jobbet. Referera också till arkivet som innehåller databas driv rutinen från `<sqoop>...</sqoop>` avsnittet i arbets flödes. xml.

För jobbet i det här dokumentet kan du till exempel använda följande steg:

1. Kopiera filen till katalogen **/tutorials/useoozie:** `mssql-jdbc-7.0.0.jre8.jar`

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Ändra för att lägga till följande XML på en ny rad ovan `</sqoop>`: `workflow.xml`

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du definierar ett Oozie-arbetsflöde och hur du kör ett Oozie-jobb. Mer information om hur du arbetar med HDInsight finns i följande artiklar:

* [Ladda upp data för Apache Hadoop jobb i HDInsight][hdinsight-upload-data]
* [Använda Apache Sqoop med Apache Hadoop i HDInsight][hdinsight-use-sqoop]
* [Använda Apache Hive med Apache Hadoop på HDInsight][hdinsight-use-hive]
* [Använda Apache gris med Apache Hadoop på HDInsight][hdinsight-use-pig]
* [Utveckla Java MapReduce-program för HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
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

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-linux-mac/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
